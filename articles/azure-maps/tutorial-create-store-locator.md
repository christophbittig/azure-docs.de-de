---
title: 'Tutorial: Erstellen von Webanwendungen für die Shopsuche mit Microsoft Azure Maps'
description: Tutorial zur Verwendung von Microsoft Azure Maps zum Erstellen von Webanwendungen für die Shopsuche.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/07/2021
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 0f8ea510000e506e4b1fd156a00b1eb89ddbb76e
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081223"
---
# <a name="tutorial-use-azure-maps-to-create-a-store-locator"></a>Tutorial: Erstellen einer Shopsuche mit Azure Maps

In diesem Tutorial wird das Erstellen einer einfachen Shopsuche mit Azure Maps beschrieben. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer neuen Webseite mit der Kartensteuerelement-API von Azure
> * Laden von benutzerdefinierten Daten aus einer Datei und Anzeigen auf einer Karte
> * Verwenden des Suchdiensts von Azure Maps zum Ermitteln einer Adresse oder Eingeben einer Abfrage
> * Abrufen des Standorts eines Benutzers im Browser und Anzeigen auf der Karte
> * Kombinieren von mehreren Ebenen zum Erstellen von benutzerdefinierten Symbolen auf der Karte  
> * Anordnen von Datenpunkten in Clustern  
> * Hinzufügen von Zoomsteuerelementen zur Karte

<a id="Intro"></a>

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen eines Azure Maps-Kontos im Tarif „Gen 1 (S1)“ oder „Gen 2“](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

Weitere Informationen zur Azure Maps-Authentifizierung finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).

In diesem Tutorial wird die Anwendung [Visual Studio Code](https://code.visualstudio.com/) verwendet. Sie können aber auch eine andere Programmierumgebung nutzen.

## <a name="sample-code"></a>Beispielcode

In diesem Tutorial erstellen Sie eine Shopsuche für ein fiktives Unternehmen mit dem Namen Contoso Coffee. Außerdem enthält das Tutorial einige Tipps, die Ihnen helfen, die Shopsuche mit weiteren optionalen Funktionen zu ergänzen.

Sie können sich das [Beispiel für eine Live-Shopsuche](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) ansehen.

Damit Sie dieses Tutorial einfach befolgen und anwenden können, müssen Sie die folgenden Ressourcen herunterladen:

* [Vollständiger Quellcode für ein einfaches Beispiel für die Shopsuche](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)
* [Daten für Shopstandorte, die in das Dataset für die Shopsuche importiert werden](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)
* [Kartendarstellungen](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images)

## <a name="store-locator-features"></a>Features der Shopsuche

In diesem Abschnitt werden die Features aufgeführt, die in der Suchanwendung für Contoso Coffee-Shops unterstützt werden.

### <a name="user-interface-features"></a>Benutzeroberflächenfunktionen

* Shoplogo in der Kopfzeile
* Karte unterstützt Schwenken und Zoomen
* Schaltfläche „My Location“ (Mein Standort) für eine Suche am aktuellen Standort des Benutzers
* Das Seitenlayout wird anhand der Breite des Gerätebildschirms angepasst.
* Ein Suchfeld und eine Suchschaltfläche

### <a name="functionality-features"></a>Funktionalität

* Mit einem hinzugefügten `keypress`-Ereignis für das Suchfeld wird eine Suche ausgelöst, wenn der Benutzer die **EINGABETASTE** drückt.
* Beim Verschieben der Karte wird die Entfernung der einzelnen Standorte vom Mittelpunkt der Karte berechnet. Die Ergebnisliste wird aktualisiert, um die in der Nähe liegenden Standorte oben auf der Karte anzuzeigen.  
* Wenn der Benutzer in der Ergebnisliste ein Ergebnis auswählt, wird der Standort als Mittelpunkt der Karte festgelegt, und in einem Popupfenster werden die zugehörigen Informationen angezeigt.  
* Wenn der Benutzer einen bestimmten Standort auswählt, löst die Karte ein Popupfenster aus.
* Wenn der Benutzer die Ansicht verkleinert, werden die Standorte in Clustern gruppiert. Jeder Cluster wird durch einen Kreis mit einer Zahl angezeigt. Cluster werden gebildet bzw. aufgelöst, wenn der Benutzer den Zoomfaktor ändert.
* Beim Auswählen eines Clusters wird die Ansicht der Karte um zwei Zoomfaktoren vergrößert, und die Position des Clusters wird in der Mitte angeordnet.

## <a name="store-locator-design"></a>Design der Shopsuche

Die folgende Abbildung enthält ein Drahtmodell des allgemeinen Layouts für die Shopsuche. Sie können das Livedrahtmodell [hier](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) anzeigen.

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe.png" alt-text="Drahtmodell der Anwendung für die Contoso Coffee-Shopsuche":::

Zur Steigerung der Nützlichkeit dieser Shopsuche fügen wir ein dynamisches Layout ein, das angepasst wird, wenn die Bildschirmbreite eines Benutzers unter 700 Pixel liegt. Ein dynamisches Layout vereinfacht die Nutzung der Shopsuche auf einem kleinen Bildschirm, z.B. bei einem mobilen Gerät. Hier ist ein Drahtmodell eines Layouts für kleine Bildschirme dargestellt:  

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-wireframe-mobile.png" alt-text="Drahtmodell der Anwendung für die Contoso Coffee-Shopsuche auf einem mobilen Gerät":::

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Erstellen des Datasets für die Standorte der Shops

In diesem Abschnitt wird beschrieben, wie Sie ein Dataset der Shops erstellen, die auf der Karte angezeigt werden sollen. Das Dataset für die Contoso Coffee-Shopsuche wird in einer Excel-Arbeitsmappe erstellt. Es enthält 10.213 Standorte von Contose Coffee-Shops in neun Ländern oder Regionen: USA, Kanada, Vereinigtes Königreich, Frankreich, Deutschland, Italien, Niederlande, Dänemark und Spanien. In diesem Screenshot ist dargestellt, wie die Daten aussehen:

:::image type="content" source="./media/tutorial-create-store-locator/store-locator-data-spreadsheet.png" alt-text="Screenshot der Daten für die Shopsuche in einer Excel-Arbeitsmappe":::

Um das vollständige Dataset anzuzeigen, [laden Sie die Excel-Arbeitsmappe hier herunter](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data).

Wenn Sie sich den Screenshot mit den Daten ansehen, fällt Ihnen Folgendes auf:

* Die Informationen zum Standort sind in den Spalten **AddressLine**, **City**, **Municipality** (Verwaltungsbezirk), **AdminDivision** (Bundesland/Kanton), **PostCode** (Postleitzahl) und **Country** enthalten.  
* Die Spalten **Latitude** (Breitengrad) und **Longitude** (Längengrad) enthalten die Koordinaten für die einzelnen Standorte von Contoso Coffee-Shops. Falls Sie keine Informationen zu Koordinaten besitzen, können Sie die Suchdienste in Azure Maps nutzen, um die Standortkoordinaten zu ermitteln.
* Einige weitere Spalten enthalten Metadaten zu den Coffee-Shops: eine Telefonnummer, boolesche Spalten sowie die Öffnungszeiten im 24-Stunden-Format. Die booleschen Spalten dienen zur Angabe von WLAN-Verfügbarkeit sowie von Barrierefreiheit für Rollstuhlfahrer. Sie können auch eigene Spalten mit Metadaten erstellen, die für Ihre Standortdaten eine höhere Relevanz haben.

> [!NOTE]
> Azure Maps rendert Daten in der sphärischen Mercator-Projektion „EPSG:3857“, liest Daten jedoch in „EPSG:4326“ mit WGS84-Bezug.

## <a name="load-the-store-location-dataset"></a>Laden des Datasets mit den Standorten der Shops

 Das Dataset für die Contoso Coffee-Shopsuche ist klein. Daher konvertieren Sie das Excel-Arbeitsblatt in eine Textdatei mit Tabstopptrennzeichen. Diese Datei kann dann im Browser heruntergeladen werden, wenn die Anwendung geladen wird.

 >[!TIP]
>Wenn Ihr Dataset zu groß für den Clientdownload ist oder häufig aktualisiert wird, können Sie das Dataset auch in einer Datenbank speichern. Nachdem Ihre Daten in eine Datenbank geladen wurden, können Sie einen Webdienst einrichten, der Abfragen für die Daten akzeptiert und die Ergebnisse dann an den Browser des Benutzers sendet.

### <a name="convert-data-to-tab-delimited-text-file"></a>Konvertieren der Daten in eine Textdatei mit Tabstopptrennzeichen

So konvertieren Sie die Standortdaten der Contoso Coffee-Shops aus einer Excel-Arbeitsmappe in eine einfache Textdatei

1. [Laden Sie die Excel-Arbeitsmappe herunter.](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data)

2. Speichern Sie die Arbeitsmappe auf Ihrer Festplatte.

3. Laden Sie die Excel-App.

4. Öffnen Sie die heruntergeladene Arbeitsmappe.

5. Wählen Sie **Speichern unter** aus.

6. Wählen Sie in der Dropdownliste **Dateityp** die Option **Text (durch Tabstopps getrennt) (*.txt)** . 

7. Geben Sie der Datei den Namen *ContosoCoffee*.

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-text.png" alt-text="Screenshot des Dialogfelds „Dateityp“":::

Wenn Sie die Textdatei in Editor öffnen, erhalten Sie eine ähnliche Anzeige wie im folgenden Text:

:::image type="content" source="./media/tutorial-create-store-locator/data-delimited-file.png" alt-text="Screenshot einer Datei in Editor mit einem Dataset mit Tabstopptrennzeichen":::

## <a name="set-up-the-project"></a>Einrichten des Projekts

1. Öffnen Sie die Visual Studio Code-App.

2. Wählen Sie **Datei** und dann **Arbeitsbereich öffnen** aus.

3. Erstellen Sie einen neuen Ordner, und geben Sie ihm den Namen „ContosoCoffee“.

4. Wählen Sie im Explorer **CONTOSOCOFFEE** aus.

5. Erstellen Sie die folgenden drei Dateien, die das Layout, den Stil und die Logik der Anwendung definieren:

    * *index.html*
    * *index.css*
    * *index.js*

6. Erstellen Sie einen Ordner mit dem Namen *data*.

7. Fügen Sie dem Ordner *data* die Datei *ContosoCoffee.txt* hinzu.

8. Erstellen Sie einen weiteren Ordner mit dem Namen *images*.

9. [Laden Sie diese 10 Bilder](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/images) herunter, falls dies noch nicht geschehen ist.

10. Fügen Sie die heruntergeladenen Bilder dem Ordner *images* hinzu.

    Ihr Arbeitsbereichsordner sollte jetzt wie im folgenden Screenshot aussehen:

    :::image type="content" source="./media/tutorial-create-store-locator/store-locator-workspace.png" alt-text="Screenshot des Arbeitsbereichsordners für die einfache Shopsuche":::

## <a name="create-the-html"></a>Erstellen des HTML-Codes

So erstellen Sie den HTML-Code

1. Fügen Sie dem `head` in der Datei *index.html* die folgenden `meta`-Tags hinzu:

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

2. Fügen Sie Verweise für die JavaScript- und CSS-Dateien des Azure Maps-Websteuerelements hinzu:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

3. Fügen Sie einen Verweis auf das Modul „Dienste“ für Azure Maps hinzu. Bei diesem Modul handelt es sich um eine JavaScript-Bibliothek, die die Azure Maps-REST-Dienste umschließt und die Nutzung in JavaScript vereinfacht. Das Modul ist als Grundlage für die Suchfunktionalität nützlich.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

4. Fügen Sie Verweise auf *index.js* und *index.css* hinzu.

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

5. Fügen Sie im Text des Dokuments ein `header`-Tag hinzu. Fügen Sie im `header`-Tag das Logo und den Unternehmensnamen hinzu.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

6. Fügen Sie ein `main`-Tag hinzu, und erstellen Sie einen Suchbereich mit einem Textfeld und einer Suchschaltfläche. Fügen Sie außerdem `div`-Verweise für die Karte, den Listenbereich und die Schaltfläche zum Anzeigen der GPS-Daten für den eigenen Standort hinzu.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

Wenn Sie fertig sind, sollte die Datei *index.html* wie in [dieser Beispieldatei „index.html“](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html) aussehen.

## <a name="define-the-css-styles"></a>Definieren der CSS-Stile

Im nächsten Schritt werden die CSS-Stile definiert. Mit CSS-Stilen wird definiert, welches Layout die Anwendungskomponenten haben und wie die Anwendung aussieht.

1. Öffnen Sie die Datei *index.css*.

2. Fügen Sie den folgenden CSS-Code hinzu:

    >[!NOTE]
    > Mit dem Stil `@media` werden alternative Stiloptionen definiert, die verwendet werden können, wenn die Bildschirmbreite weniger als 700 Pixel beträgt.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    } 

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is fewer than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Führen Sie die Anwendung aus. Die Kopfzeile, das Suchfeld und die Suchschaltfläche werden angezeigt. Die Karte ist jedoch nicht sichtbar, da sie noch nicht geladen wurde. Wenn Sie versuchen, eine Suche durchzuführen, passiert nichts. Wir müssen die JavaScript-Logik einrichten. Dies wird im nächsten Abschnitt beschrieben. Von dieser Logik wird auf sämtliche Funktionen der Shopsuche zugegriffen.

## <a name="add-javascript-code"></a>Hinzufügen von JavaScript-Code

Der JavaScript-Code in der Contoso Coffee-Shopsuche-App führt die folgenden Prozesse aus:

1. Er fügt einen [Ereignislistener](/javascript/api/azure-maps-control/atlas.map#events) mit dem Namen `ready` hinzu, der wartet, bis der Ladevorgang der Seite abgeschlossen ist. Wenn die Seite vollständig geladen wurde, erstellt der Ereignishandler weitere Ereignislistener, um das Laden der Karte zu überwachen, und stellt Funktionen für die Schaltflächen „Suchen“ und **My location** (Mein Standort) zur Verfügung.

2. Wenn der Benutzer die Suchschaltfläche auswählt oder im Suchfeld einen Standort eingibt und danach die EINGABETASTE drückt, wird für die Abfrage des Benutzers eine Fuzzysuche initiiert. Der Code übergibt ein Array mit ISO 2-Länder-/Regionswerten an die Option `countrySet`, um die Suchergebnisse auf diese Länder/Regionen zu beschränken. Durch das Begrenzen der zu durchsuchenden Länder/Regionen kann die Genauigkeit der zurückgegebenen Ergebnisse erhöht werden.
  
3. Nach Abschluss der Suche wird das erste Standortergebnis als Mittelpunkt der Kartenkamera verwendet. Wenn der Benutzer die Schaltfläche zum Anzeigen des eigenen Standorts auswählt, ruft der Code den Benutzerstandort mithilfe der *HTML5-Geolocation-API* ab, die in den Browser integriert ist. Nach dem Abrufen des Standorts zentriert der Code die Karte über dem Standort des Benutzers.  

So fügen Sie den JavaScript-Code hinzu

1. Öffnen Sie *index.js*.

2. Fügen Sie globale Optionen hinzu, um das Aktualisieren der Einstellungen zu vereinfachen. Definieren Sie die Variablen für die Karte, das Popupfenster, die Datenquelle, die Symbolebene und den HTML-Marker. Legen Sie den HTML-Marker fest, um den Mittelpunkt eines Suchbereichs anzugeben. Definieren Sie eine Instanz des Azure Maps-Suchdienstclients.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

3. Fügen Sie den folgenden Initialisierungscode hinzu. Stellen Sie sicher, dass Sie `<Your Azure Maps Key>` durch Ihren primären Abonnementschlüssel ersetzen.

   > [!Tip]
   > Beim Verwenden von Popupfenstern ist es am besten, eine einzelne `Popup`-Instanz zu erstellen und diese wiederzuverwenden, indem Inhalt und Position aktualisiert werden. Für jede `Popup`-Instanz, die Sie Ihrem Code hinzufügen, werden der Seite mehrere DOM-Elemente hinzugefügt. Je mehr DOM-Elemente sich auf einer Seite befinden, desto mehr Elemente muss der Browser nachverfolgen. Wenn zu viele Elemente vorhanden sind, kann es sein, dass der Browser langsam wird.

    ```JavaScript

    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API (Preview) to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country/region ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country/region ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API (Preview) position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

4. Fügen Sie im Ereignislistener `ready` der Karte ein Zoomsteuerelement und einen HTML-Marker hinzu, um den Mittelpunkt eines Suchbereichs anzuzeigen.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

5. Fügen Sie im Ereignislistener `ready` der Karte eine Datenquelle hinzu. Führen Sie anschließend einen Aufruf durch, und analysieren Sie das Dataset. Aktivieren Sie das Clustering für die Datenquelle. Beim Clustering auf der Datenquelle werden sich überlappende Punkte in einem Cluster gruppiert. Wenn der Benutzer die Ansicht vergrößert, werden die Cluster in einzelne Punkte unterteilt. Dieses Verhalten ermöglicht eine höhere Benutzerfreundlichkeit und verbessert die Leistung.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

6. Nachdem das Dataset in den Ereignislistener `ready` der Karte geladen wurde, definieren Sie eine Gruppe mit Ebenen zum Rendern der Daten. Auf einer Blasenebene werden gruppierte Datenpunkte gerendert. Eine Symbolebene wird zum Rendern der Anzahl von Punkten in jedem Cluster oberhalb der Blasenebene verwendet. Über eine zweite Symbolebene wird ein benutzerdefiniertes Symbol für einzelne Standorte auf der Karte gerendert.

   Fügen Sie die Ereignisse `mouseover` und `mouseout` der Blasen- und Symbolebene hinzu, damit sich der Mauszeiger ändert, wenn der Benutzer die Maus in der Karte auf einen Cluster oder ein Symbol bewegt. Fügen Sie der Clusterblasenebene das Ereignis `click` hinzu. Mit diesem Ereignis vom Typ `click` wird die Karte um zwei Zoomstufen vergrößert und über einem Cluster zentriert, wenn der Benutzer einen Cluster auswählt. Fügen Sie der Symbolebene ein `click`-Ereignis hinzu. Mit diesem `click`-Ereignis wird ein Popupfenster angezeigt, in dem die Details zu einem Coffee-Shop eingeblendet werden, wenn ein Benutzer ein Symbol für einen Standort wählt. Fügen Sie der Karte ein Ereignis hinzu, um zu überwachen, wann die Verschiebung der Karte abgeschlossen ist. Aktualisieren Sie die Elemente im Listenbereich, wenn dieses Ereignis ausgelöst wird.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

       //Create a layer to render a coffee cup symbol above each bubble for an individual location.
       iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
           iconOptions: {
               //Pass in the ID of the custom icon that was loaded into the map resources.
               image: 'myCustomIcon',

               //Optionally, scale the size of the icon.
               font: ['SegoeUi-Bold'],

               //Anchor the center of the icon image to the coordinate.
               anchor: 'center',

               //Allow the icons to overlap.
               allowOverlap: true
           },

           filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
       });

       map.layers.add(iconLayer);

       //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
       map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
           map.getCanvasContainer().style.cursor = 'pointer';
       });

       //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
       map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
           map.getCanvasContainer().style.cursor = 'grab';
       });

       //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
       map.events.add('click', clusterBubbleLayer, function(e) {
           map.setCamera({
               center: e.position,
               zoom: map.getCamera().zoom + 2
           });
       });

       //Add a click event to the icon layer and show the shape that was selected.
       map.events.add('click', iconLayer, function(e) {
           showPopup(e.shapes[0]);
       });

       //Add an event to monitor when the map is finished rendering the map after it has moved.
       map.events.add('render', function() {
           //Update the data in the list.
           updateListItems();
       });
    });
    ```

7. Nachdem das Coffee-Shop-Dataset geladen wurde, muss zuerst der Download durchgeführt werden. Anschließend muss die Textdatei in Zeilen unterteilt werden. Die erste Zeile enthält die Kopfzeileninformationen. Damit der Code leichter verfolgt werden kann, analysieren wir das Kopfzeilensymbol in ein Objekt, das wir dann nutzen können, um den Zellenindex der einzelnen Eigenschaften nachzuschlagen. Führen Sie nach der ersten Zeile eine Schleife durch die restlichen Zeilen durch, und erstellen Sie ein Punktfeature. Fügen Sie das Punktfeature der Datenquelle hinzu. Aktualisieren Sie abschließend den Listenbereich.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

8. Beim Aktualisieren des Listenbereichs wird die Entfernung berechnet. Hierbei handelt es sich um die Entfernung vom Mittelpunkt der Karte zu allen Punktfeatures in der aktuellen Kartenansicht. Die Features werden dann nach Entfernung sortiert. Es wird HTML-Code generiert, um die einzelnen Standorte im Listenbereich anzuzeigen.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

9. Wenn der Benutzer im Listenbereich ein Element auswählt, wird die Form, zu der das Element gehört, aus der Datenquelle abgerufen. Es wird ein Popupfenster generiert, das auf den in der Form gespeicherten Eigenschafteninformationen basiert. Die Karte wird über der Form zentriert. Falls die Karte weniger als 700 Pixel breit ist, wird die Kartenansicht versetzt, damit das Popupfenster sichtbar ist.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is fewer than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Sie verfügen jetzt über eine voll funktionsfähige Shopsuche. Öffnen Sie in einem Webbrowser die Datei *index.html* für die Shopsuche. Wenn die Cluster in der Karte angezeigt werden, können Sie nach einem Standort suchen, indem Sie das Suchfeld verwenden, die Schaltfläche zum Anzeigen des eigenen Standorts wählen, einen Cluster auswählen oder die Karte vergrößern, um einzelne Standorte anzuzeigen.

Bei der ersten Verwendung der Schaltfläche zum Anzeigen des eigenen Standorts durch einen Benutzer wird im Browser eine Sicherheitswarnung angezeigt und um die Zustimmung für den Zugriff auf den Standort des Benutzers gebeten. Wenn der Benutzer der Freigabe seines Standorts zustimmt, wird der Bereich mit dem Benutzerstandort in der Karte vergrößert, und die in der Nähe befindlichen Coffee-Shops werden angezeigt.

![Screenshot: Anforderung des Zugriffs auf den Benutzerstandort im Browser](./media/tutorial-create-store-locator/geolocation-api-warning.png)

Wenn Sie einen Bereich stark genug vergrößern, der Coffee-Shop-Standorte enthält, werden die Cluster in einzelne Standorte aufgeteilt. Wählen Sie eines der Symbole in der Karte aus, oder wählen Sie ein Element auf der Seitenleiste aus, um ein Popupfenster anzuzeigen. Im Popupfenster werden Informationen zum ausgewählten Standort angezeigt.

![Screenshot: Fertige Shopsuche](./media/tutorial-create-store-locator/finished-simple-store-locator.png)

Wenn Sie die Größe des Browserfensters auf eine Breite von weniger als 700 Pixel verkleinern oder die App auf einem mobilen Gerät öffnen, ändert sich die Anzeige in ein Layout, das für kleinere Bildschirme besser geeignet ist.

![Screenshot: Version der Shopsuche für kleinere Bildschirme](./media/tutorial-create-store-locator/finished-simple-store-locator-mobile.png)

In diesem Tutorial wurde beschrieben, wie Sie mit Azure Maps eine einfache Shopsuche erstellen. Die in diesem Tutorial erstellte Shopsuche verfügt unter Umständen bereits über alle Funktionen, die Sie benötigen. Sie können der Shopsuche aber Features hinzufügen oder erweiterte Features verwenden, um eine benutzerdefinierte Benutzeroberfläche zu erzielen: 

 * Aktivieren Sie im Suchfeld [Vorschläge während der Eingabe](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI).  
 * Fügen Sie [Unterstützung für mehrere Sprachen](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization) hinzu. 
 * Ermöglichen Sie für Benutzer das [Filtern von Standorten anhand einer Route](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route). 
 * Fügen Sie die Option zum [Festlegen von Filtern](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property) hinzu. 
 * Fügen Sie Unterstützung für das Angeben eines Anfangssuchwerts hinzu, indem Sie eine Abfragezeichenfolge verwenden. Wenn Sie diese Option in Ihre Shopsuche einfügen, können Benutzer Lesezeichen für Suchen festlegen und Suchvorgänge teilen. Außerdem ist dies eine einfache Methode, mit der Sie Suchen von einer anderen Seite an diese Seite übergeben können.  
 * Stellen Sie Ihre Shopsuche als [Azure App Service-Web-App](../app-service/quickstart-html.md) bereit. 
 * Speichern Sie Ihre Daten in einer Datenbank, und suchen Sie nach Standorten in der Nähe. Weitere Informationen finden Sie unter [Übersicht über räumliche Datentypen](/sql/relational-databases/spatial/spatial-data-types-overview?preserve-view=true&view=sql-server-2017) und [Abfragen von nächsten Nachbarn aus räumlichen Daten](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?preserve-view=true&view=sql-server-2017).

Sie können [hier den vollständigen Quellcode anzeigen](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator). [Hier können Sie ein Livebeispiel anzeigen](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator) und mehr über die abgedeckten Bereiche und die Funktionen von Azure Maps erfahren, die mit [Zoomstufen und dem Kachelraster](zoom-levels-and-tile-grid.md) verfügbar sind. Sie können ferner [datengestützte Formatvorlagenausdrücke verwenden](data-driven-style-expressions-web-sdk.md), um sie auf Ihre Geschäftslogik anzuwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es sind keine zu bereinigenden Ressourcen vorhanden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Codebeispiele und eine Benutzeroberfläche für das interaktive Codieren:

> [!div class="nextstepaction"]
> [Verwenden des Kartensteuerelements](how-to-use-map-control.md)
