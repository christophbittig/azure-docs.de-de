---
title: Verwenden des Video Analyzer-Playerwidgets
description: In diesem Artikel wird erläutert, wie Sie Ihrer Anwendung ein Video Analyzer-Playerwidget hinzufügen.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/12/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8dbf85609196a930d7f51a713753f725b3016396
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487471"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Verwenden des Azure Video Analyzer-Player-Widgets

In diesem Tutorial erfahren Sie, wie Sie ein Player-Widget in Ihrer Anwendung verwenden können. Bei diesem Code handelt es sich um einen leicht einzubettendes Widget, das es Ihren Endbenutzern ermöglicht, Videos wiederzugeben und durch Teile einer segmentierten Videodatei zu navigieren. Dazu erstellen Sie eine statische HTML-Seite, in die das Widget eingebettet ist und die alle notwendigen Elemente enthält, damit es funktioniert.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Seite mit dem Player
> * Listen Sie Videos auf
> * Übergeben eines Streamingendpunkts und eines Tokens an den Player
> * Hinzufügen eines Zone Drawer-Players
> * Anzeigen von Videos, die auf die angegebenen Start- und Endzeiten getrimmt wurden

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
* [Visual Studio Code](https://code.visualstudio.com/) oder einen anderen Editor für die HTML-Datei.
* Entweder [kontinuierliche Videoaufzeichnung und -Wiedergabe](edge/use-continuous-video-recording.md) oder [Bewegungserkennung und Videoaufzeichnung auf Edge-Geräten](./detect-motion-record-video-clips-cloud.md)
* Erstellen eines [Tokens](./access-policies.md#creating-a-token)
* Erstellen einer [Zugriffsrichtlinie](./access-policies.md#creating-an-access-policy)


## <a name="create-a-web-page-with-a-video-player"></a>Erstellen einer Webseite mit einem Videoplayer

Verwenden Sie den folgenden Beispielcode, um eine Webseite zu erstellen.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
</body>
</body>
</html>
```
## <a name="list-video-resources"></a>Auflisten von Videoressourcen

Als nächstes generieren Sie eine Liste von Videoressourcen. Sie führen einen REST-Aufruf für den zuvor verwendeten Kontoendpunkt durch und authentifizieren sich mit dem von Ihnen generierten Token.

Es sind viele Möglichkeiten vorhanden, eine GET-Anforderung an eine REST-API zu senden, dafür verwenden Sie jedoch eine JavaScript-Funktion. Der folgende Code verwendet [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) in Verbindung mit Werten, die wir in den Feldern `clientApiEndpointUrl` und `token` auf der Seite speichern, um eine synchrone `GET`-Anforderung zu senden. Anschließend wird die resultierende Liste der Videos verwendet und im `videoList`-Textbereich gespeichert, den Sie auf der Seite eingerichtet haben.

Der folgende Codeausschnitt hilft beim Anfordern der Videoliste.

```javascript
function getVideos()
{
    var xhttp = new XMLHttpRequest();
    var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
    xhttp.open("GET", getUrl, false);
    xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
    xhttp.send();
    document.getElementById("videoList").value = xhttp.responseText.toString();
}
```
   > [!NOTE]
   >Die `clientApiEndPoint` und `token` werden beim [Erstellen eines Tokens](./access-policies.md#creating-a-token) gesammelt

## <a name="add-the-video-analyzer-player-component"></a>Hinzufügen der Video Analyzer-Playerkomponente

Da Sie nun über eine Client-API-Endpunkt-URL, ein Token und einen Videonamen verfügen, können Sie den Player zur Seite hinzufügen.

1. Schließen Sie das Player-Modul selbst ein, indem Sie das Paket direkt zu Ihrer Seite hinzufügen. Sie können entweder die NPM-Paketrichtung in Ihre Anwendung einbinden oder zur Laufzeit dynamisch einbetten lassen, wie im Folgenden zu sehen:
   ```html
   <script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
   ```
1. Fügen Sie dem Dokument ein `AVA-Player`-Element hinzu:
   ```html
   <ava-player width="720px" id="avaPlayer"></ava-player>
   ```
1. Rufen Sie einen Link zum Video Analyzer-Player-Widget ab, das sich auf der Seite befindet:
   ```javascript
   const avaPlayer = document.getElementById("videoPlayer");
   ```
1. Um den Player mit den Werten zu konfigurieren, über die Sie verfügen, müssen Sie die Werte wie hier angezeigt als Objekt einrichten:
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Laden Sie das Video in den Player, um zu beginnen:
   ```javascript
   avaPlayer.load();
   ```
   
## <a name="add-the-zone-drawer-component"></a>Hinzufügen der Zone Drawer-Komponente

Mit der Komponente Zone Drawer-Komponente können Sie Linien und Polygone über dem Video Analyzer-Player zeichnen. 

1. Fügen Sie dem Dokument ein AVA-Zonenzeichnerelement hinzu:
   ```html
   <ava-zone-drawer width="720px" id="zoneDrawer">
        <ava-player id="videoPlayer2"></ava-player>
   </ava-zone-drawer>
   ```
1. Abrufen eines Links zum Video Analyzer-Playerwidget, das im Zone Drawer wiedergegeben wird:
   ```javascript
   const avaPlayer2 = document.getElementById("videoPlayer2");
   ```
1. Konfigurieren Sie den Player, der im Zone Player arbeitet:
   ```javascript
   avaPlayer2.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Laden Sie das Video in den Player innerhalb des Zone Drawer:
   ```javascript
   avaPlayer2.load();
   ```
1. Abrufen eines Links zum Zone Drawer, der sich auf der Seite befindet:
   ```javascript
   const zoneDrawer = document.getElementById("zoneDrawer");
   ```
1. Laden Sie den Zonenzeichner in den Player:
   ```javascript
   zoneDrawer.load();
   ```
1. Konfigurieren Sie den Zone Drawer:
   ```javascript
   zoneDrawer.configure();
   ```
1. Zum Erstellen und Speichern von Zonen müssen Sie hier Ereignislistener hinzufügen:
   ```javascript
   zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
   ```

## <a name="put-it-all-together"></a>Korrektes Zusammenfügen

Wenn Sie die vorangehenden Webelemente kombinieren, erhalten Sie die folgende statische HTML-Seite. Auf dieser Seite können Sie einen Kontoendpunkt und ein Token verwenden, um ein Video anzuzeigen.

```html
<html>
<head>
<title>Video Analyzer Player Widget Demo</title>
</head>
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets"></script>
<body>
<script>
    function getVideos()
    {
        var xhttp = new XMLHttpRequest();
        var getUrl = document.getElementById("clientApiEndpointUrl").value + "/videos?api-version=2021-05-01-preview";
        xhttp.open("GET", getUrl, false);
        xhttp.setRequestHeader("Authorization", "Bearer " + document.getElementById("token").value);
        xhttp.send();
        document.getElementById("videoList").value = xhttp.responseText.toString();
    }
    function playVideo() {
        const avaPlayer = document.getElementById("videoPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
        
        const avaPlayer2 = document.getElementById("videoPlayer2");
        avaPlayer2.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer2.load();
    
        const zoneDrawer = document.getElementById("zoneDrawer");
        zoneDrawer.load();
        zoneDrawer.configure();

        zoneDrawer.addEventListener('ZONE_DRAWER_ADDED_ZONE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });

        zoneDrawer.addEventListener('ZONE_DRAWER_SAVE', (event) => {
            console.log(event);
            document.getElementById("zoneList").value = JSON.stringify(event.detail);
        });
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
JWT Auth Token for Client API: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<div id="container" style="width:720px" class="widget-container">
    <ava-player width="720px" id="videoPlayer"></ava-player>
</div>
<textarea rows="5" cols="100" id="zoneList"></textarea><br><br>
<ava-zone-drawer width="720px" id="zoneDrawer">
    <ava-player id="videoPlayer2"></ava-player>
</ava-zone-drawer>
</body>
</html>
```

## <a name="host-the-page"></a>Hosten der Seite

Sie können diese Seite lokal testen, aber vielleicht möchten Sie auch eine gehostete Version testen. Falls Sie über keine schnelle Möglichkeit zum Hosten einer Seite verfügen, finden Sie hier Anweisungen zur Verwendung [statischer Websites](../../storage/blobs/storage-blob-static-website.md) mit Azure Storage. Die folgenden Schritte sind eine gekürzte Version [dieser ausführlicheren Anweisungen](../../storage/blobs/storage-blob-static-website-how-to.md). Die Schritte werden für die Dateien aktualisiert, die Sie in diesem Tutorial verwenden.

1. Erstellen Sie ein Speicherkonto.
1. Wählen Sie unter **Datenverwaltung** die Option **Statische Website** aus.
1. Aktivieren Sie die statische Website für das Speicherkonto.
1. Geben Sie **index.html** als **Name des Indexdokuments** ein.
1. Für **Pfad zum Fehlerdokument** geben Sie **404.html** ein.
1. Wählen Sie **Speichern** aus.
1. Beachten Sie den **primären Endpunkt**, der angezeigt wird. Dies wird Ihre Website sein.
1. Wählen Sie über **Primärer Endpunkt** die Option **$web** aus.
1. Verwenden Sie mithilfe der Schaltfläche **Hochladen** oben Ihre statische HTML-Seite als **index.html** hoch.

### <a name="play-a-video"></a>Spielen Sie ein Video ab

Nachdem Sie jetzt die Seite gehostet haben, wechseln Sie dorthin und führen Sie die Schritte aus, um ein Video wiederzugeben.

1. Geben Sie die Werte **Endpunkt-URL der Client-API** und **Token** ein.
1. Wählen Sie **Videos abrufen** aus.
1. Wählen Sie in der Videoliste einen Videonamen aus, und geben Sie ihn in das Feld **Videoname** ein.
1. Wählen Sie **Video wiedergeben** aus.

### <a name="live-video-playback"></a>Live-Videowiedergabe

Wenn sich Ihre livePipeline in einem `activated` Zustand befindet und das Video aufgezeichnet wird, lädt der Player automatisch die **LIVE**-Ansicht. Diese Videowiedergabe erfolgt nahezu in Echtzeit und weist eine kurze Latenz von etwa 2 Sekunden auf.

In der **LIVE**-Ansicht haben Sie Folgendes:
1. Sehen Sie sich die Videowiedergabe nahezu in Echtzeit an.
1. Die Zeitachse wird nicht angezeigt.
1. Wenn Sie auf das **Box**-Symbol klicken, werden etwa vorhandene Begrenzungsfelder angezeigt.

> [!Tip]
> Um zur Ansicht zu wechseln, in der Sie alle zuvor aufgezeichneten Clips anzeigen können, klicken Sie auf die Schaltfläche **LIVE.**
 
### <a name="capture-lines-and-zones"></a>Erfassen von Linien und Zonen

1. Navigieren Sie zum **Zone Drawer**-Player
1. Klicken Sie in der oberen linken Ecke auf das erste Symbol, um Zonen zu zeichnen.
1. Um Zonen und Linien zu zeichnen, müssen Sie nur auf die Positionen für die gewünschten Endpunkte klicken. Es gibt keine Ziehfunktionalität, um die Zonen und Linien zu zeichnen.
1. Im rechten Abschnitt des Players werden die erstellten Zonen und Linien angezeigt.
1. Um die Koordinaten der Linien und Zonen abzurufen, klicken Sie auf die Schaltfläche **Speichern**.
1. Auf diese Weise wird die JSON-Antwort mit den Punktkoordinaten angezeigt, die Sie mit den entsprechenden Topologien verwenden können.

### <a name="video-clips"></a>Videoclips
Ermöglicht das Erstellen von Videoclips durch Auswahl einer Start- und Endzeit.

Das Video Analyzer-Videoplayerwidget unterstützt die Wiedergabe von Videoclips, indem, wie nachstehend gezeigt, eine Start- und Enddatumszeit angegeben wird:

> [!Note] 
> Das Video Analyzer-Videoplayerwidget verwendet den UTC-Zeitstandard. Daher muss die ausgewählte Start- und Endzeit in dieses Format konvertiert werden.

Verwenden Sie den folgenden Code in Ihrer HTML-Datei, um einen Videoplayer zu öffnen, der ein Video aus startTime und endTime lädt, die Sie angeben.

```javascript
    const avaPlayer = document.getElementById("videoPlayer");
    const startUTCDate = new Date(Date.UTC(selectedClip.start.getFullYear(), selectedClip.start.getMonth(), selectedClip.start.getDate(), selectedClip.start.getHours(), selectedClip.start.getMinutes(), selectedClip.start.getSeconds()));
    const endUTCDate = new Date(Date.UTC(selectedClip.end.getFullYear(), selectedClip.end.getMonth(), selectedClip.end.getDate(), selectedClip.end.getHours(), selectedClip.end.getMinutes(), selectedClip.end.getSeconds()));
    avaPlayer.load({ startTime: startUTCDate, endTime: endUTCDate });
``` 

## <a name="additional-details"></a>Zusätzliche Details

Die folgenden Abschnitte enthalten einige wichtige zusätzliche Details, die Sie beachten sollten.
### <a name="refresh-the-access-token"></a>Aktualisieren des Zugriffstokens

Der Player verwendet das Zugriffstoken, das Sie zuvor erstellt haben, um ein Autorisierungstoken für die Wiedergabe abzurufen. Token laufen in regelmäßigen Abständen ab und müssen aktualisiert werden. Es gibt zwei Möglichkeiten, das Zugriffstoken für den Player zu aktualisieren, nachdem Sie ein neues generiert haben:

* Aktiv die Widget-Methode `setAccessToken` aufrufen.
    ```typescript
    avaPlayer.setAccessToken('<NEW-ACCESS-TOKEN>');
    ```
* Auf das `TOKEN_EXPIRED`-Ereignis durch Lauschen auf dieses Ereignis reagieren.
    ```typescript
    avaPlayer.addEventListener(PlayerEvents.TOKEN_EXPIRED, () => {
        avaPlayer.setAccessToken('<YOUR-NEW-TOKEN>');
    });
    ```

Das `TOKEN_EXPIRED`-Ereignis tritt 5 Sekunden vor Ablauf des Tokens auf. Wenn Sie einen Ereignislistener festlegen, sollten Sie dies durchführen, bevor Sie die `load`-Funktion für das Player-Widget aufrufen.

### <a name="configuration-details"></a>Konfigurationsdetails

Der vorangehende Player weist eine einfache Konfiguration auf, aber Sie können eine breitere Palette von Optionen für Konfigurationswerte verwenden. Die folgenden Felder werden unterstützt:

| Name   | type             | Beschreibung                         |
| ------ | ---------------- | ----------------------------------- |
| `token`  | Zeichenfolge | Ihr JWT-Token für das Widget |
| `videoName` | Zeichenfolge | Die Bezeichnung der Video-Ressource  |
| `clientApiEndpointUrl` | Zeichenfolge | Die Endpunkt-URL für die Client-API |

### <a name="alternate-ways-to-load-the-code-into-your-application"></a>Alternative Möglichkeiten zum Laden des Codes in Ihre Anwendung

Das Paket, das zum Herunterladen des Codes in Ihre Anwendung verwendet wird, ist ein [NPM-Paket](https://www.npmjs.com/package/@azure/video-analyzer-widgets). Im vorherigen Beispiel wurde die neueste Version zur Laufzeit direkt aus dem Repository geladen. Sie können das Paket jedoch auch lokal herunterladen und installieren, indem Sie Folgendes verwenden:

```bash
npm install @azure/video-analyzer/widgets
```

Sie können es auch in Ihren Anwendungscode importieren, indem Sie Folgendes für TypeScript verwenden:

```typescript
import { Player } from '@azure/video-analyzer-widgets';
import { ZoneDrawer } from '@azure/video-analyzer-widgets';
```

Wenn Sie ein Player-Widget dynamisch erstellen möchten, können Sie Folgendes für JavaScript verwenden:
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```


Wenn Sie diese Methode zum Importieren verwenden, müssen Sie die Zonenzeichner- und Playerobjekt programmgesteuert erstellen, nachdem der Import abgeschlossen ist.  Im vorherigen Beispiel haben Sie das Modul mithilfe des `ava-player`-HTML-Tags zur Seite hinzugefügt. Um ein Zonenzeichner- und Playerobjekt mithilfe von Code zu erstellen, können Sie in JavaScript wie folgt vorgehen:


```javascript
const zoneDrawer = new window.ava.widgets.zoneDrawer();
document.firstElementChild.appendChild(zoneDrawer);
const playerWidget = new window.ava.widgets.player();
zoneDrawer.appendChild(playerWidget);
```

Oder in TypeScript:

```typescript
const avaPlayer = new Player();
const zoneDrawer = new ZoneDrawer();
```

Anschließend müssen Sie es dem HTML-Code hinzufügen:

```javascript
document.firstElementChild.appendChild(zoneDrawer);
zoneDrawer.appendChild(playerWidget);
```

## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie unsere [Beispielwiedergabe mithilfe von Widgets](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp/tree/main/src/video-player) aus
* Informationen dazu, wie die verschiedenen Widgetfeatures implementiert werden können, finden Sie in unserer [Widget-Bibliothek](https://github.com/Azure/video-analyzer-widgets).
