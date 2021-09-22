---
title: Verwenden des Azure Video Analyzer-Player-Widgets
description: In diesem Referenzartikel wird erläutert, wie Sie Ihrer Anwendung ein Video Analyzer-Player-Widget hinzufügen.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: b70bfc9a10e357c6f1e64c1737fdb4c049b505f5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037442"
---
# <a name="use-the-azure-video-analyzer-player-widget"></a>Verwenden des Azure Video Analyzer-Player-Widgets

In diesem Tutorial erfahren Sie, wie Sie ein Player-Widget in Ihrer Anwendung verwenden können. Bei diesem Code handelt es sich um einen leicht einzubettendes Widget, das es Ihren Endbenutzern ermöglicht, Videos wiederzugeben und durch Teile einer segmentierten Videodatei zu navigieren. Dazu erstellen Sie eine statische HTML-Seite, in die das Widget eingebettet ist und die alle notwendigen Elemente enthält, damit es funktioniert.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein Token
> * Listen Sie Videos auf
> * Erhalten Sie die Basis-URL zur Wiedergabe einer [Videoanwendungsressource](./terminology.md#video)
> * Erstellen Sie eine Seite mit dem Player
> * Übergeben eines Streamingendpunkts und eines Tokens an den Player

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
* [Visual Studio Code](https://code.visualstudio.com/) oder einen anderen Editor für die HTML-Datei.
* Entweder [kontinuierliche Videoaufzeichnung und -Wiedergabe](./use-continuous-video-recording.md) oder [Bewegungserkennung und Videoaufzeichnung auf Edgegeräten](./detect-motion-record-video-clips-cloud.md).

Darüber hinaus ist es hilfreich, sich mit den folgenden Ressourcen vertraut zu machen:

- [Webkomponenten](https://developer.mozilla.org/docs/Web/Web_Components)
- [TypeScript](https://www.typescriptlang.org)

## <a name="create-a-token"></a>Erstellen Sie ein Token

In diesem Abschnitt erstellen Sie ein JSON Web Token (JWT), das Sie später in diesem Artikel verwenden werden. Sie verwenden eine Beispielanwendung, die das JWT-Token erstellt und Ihnen alle Felder zur Verfügung stellt, die zum Erstellen der Zugriffsrichtlinie erforderlich sind.

> [!NOTE] 
> Wenn Sie mit dem Generieren eines JWT-Tokens auf der Grundlage eines RSA- oder ECC-Zertifikats vertraut sind, können Sie diesen Abschnitt überspringen.

1. Klonen Sie das [Repository mit AVA-C#-Beispielen](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). Wechseln Sie dann in den Ordner *src/jwt-token-issuer*, und suchen Sie die Anwendung *JWTTokenIssuer*.

    > [!NOTE] 
    > Weitere Informationen zum Konfigurieren Ihrer Zielgruppenwerte finden Sie unter [Zugriffsrichtlinien](./access-policies.md).

2. Öffnen Sie Visual Studio Code, und wechseln Sie in den Ordner, in den Sie die Anwendung *JWTTokenIssuer* heruntergeladen haben. Dieser Ordner sollte die *\*CSPROJ*-Datei enthalten.
3. Wechseln Sie im Explorer-Bereich zur Datei *program.cs*.
4. Ändern Sie in Zeile 77 die Zielgruppe in Ihren Azure Video Analyzer-Endpunkt, gefolgt von „/videos/\*“. Die Anzeige sollte folgendermaßen aussehen:

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Sie finden den Video Analyzer-Endpunkt im Abschnitt „Übersicht“ der Video Analyzer-Ressource im Azure-Portal. Auf diesen Wert wird in der [Liste der Videoressourcen](#list-video-resources) weiter unten in diesem Artikel als `clientApiEndpointUrl` verwiesen.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Screenshot: Endpunkt des Player-Widgets":::
    
5. Ändern Sie in Zeile 78 den Aussteller in den Ausstellerwert Ihres Zertifikats (Beispiel: `https://contoso.com`).
6. Speichern Sie die Datei . Möglicherweise werden Sie mit der folgenden Meldung aufgefordert: **Erforderliche Ressource zum Erstellen und Debuggen fehlen im „JWT-Tokenaussteller“. Möchten Sie diese hinzufügen?** Wählen Sie **Ja**.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Screenshot: Eingabeaufforderung für erforderliche Ressource in Visual Studio Code":::
   
7. Öffnen Sie das Eingabeaufforderungsfenster, und wechseln Sie in den Ordner mit den *JWTTokenIssuer*-Dateien. Führen Sie die folgenden beiden Befehle aus: `dotnet build`, gefolgt von `dotnet run`. Wenn Sie über die C#-Erweiterung für Visual Studio Code verfügen, können Sie auch F5 auswählen, um die *JWTTokenIssuer*-Anwendung auszuführen.

Die Anwendung wird erstellt und ausgeführt. Nach dem Erstellen erstellt es ein selbstsigniertes Zertifikat und generiert die JWT-Tokeninformationen aus diesem Zertifikat. Sie können auch die Datei *JWTTokenIssuer.exe* ausführen, die sich im Debugordner des Verzeichnisses befindet, in dem *JWTTokenIssuer* erstellt wurde. Der Vorteil der Ausführung der Anwendung besteht darin, dass Sie Eingabeoptionen wie folgt angeben können:

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

*JWTTokenIssuer* erstellt das JWT und die folgenden erforderlichen Komponenten:

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

**Achten Sie darauf, diese Werte zur späteren Verwendung zu kopieren und zu speichern.**

## <a name="create-an-access-policy"></a>Erstellen einer Zugriffsrichtlinie

Die Zugriffsrichtlinien bestimmen die Berechtigungen und die Dauer des Zugriffs auf einen bestimmten Videostream. Für dieses Tutorial konfigurieren Sie eine Zugriffsrichtlinie für Video Analyzer im Azure-Portal.  

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer Ressourcengruppe, in der sich Ihr Video Analyzer-Konto befindet.
1. Wählen Sie die Azure Video Analyzer-Ressource aus.
1. Wählen Sie unter **Video Analyzer** die Option **Zugriffsrichtlinien** aus.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Screenshot: Option „Zugriffsrichtlinien“":::
   
1. Wählen Sie **Neu** aus, und geben Sie die folgenden Informationen ein:

   - **Name der Zugriffsrichtlinie**: Sie können einen beliebigen Namen auswählen.

   - **Aussteller**: Dieser Wert muss mit dem JWT-Aussteller übereinstimmen. 

   - **Zielgruppe**: Die Zielgruppe für das JWT. `${System.Runtime.BaseResourceUrlPattern}` ist die Standardeinstellung. Weitere Informationen über die Zielgruppe und `${System.Runtime.BaseResourceUrlPattern}` finden Sie unter [Zugriffsrichtlinien](./access-policies.md).

   - **Schlüsseltyp**: RSA 

   - **Algorithmus**: Die unterstützten Werte sind RS256, RS384, RS512.

   - **Schlüssel-ID**: Diese ID wird aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#create-a-token).

   - **RSA-Schlüsselmodulus**: Dieser Wert wird aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#create-a-token).

   - **RSA-Schlüsselexponent**: Dieser Wert wird aus Ihrem Zertifikat generiert. Weitere Informationen finden Sie unter [Erstellen eines Tokens](#create-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Screenshot: Portal für Zugriffsrichtlinien"::: 
   
   > [!NOTE] 
   > Diese Werte entstammen der *JWTTokenIssuer*-Anwendung, die im vorherigen Schritt erstellt wurde.

1. Wählen Sie **Speichern** aus.

## <a name="list-video-resources"></a>Auflisten von Videoressourcen

Als nächstes generieren Sie eine Liste von Videoressourcen. Sie führen einen REST-Aufruf für den zuvor verwendeten Kontoendpunkt durch und authentifizieren sich mit dem von Ihnen generierten Token.

Es sind viele Möglichkeiten vorhanden, eine GET-Anforderung an eine REST-API zu senden, dafür verwenden Sie jedoch eine JavaScript-Funktion. Der folgende Code verwendet [XMLHttpRequest](https://www.w3schools.com/xml/ajax_xmlhttprequest_create.asp) in Verbindung mit Werten, die wir in den Feldern `clientApiEndpointUrl` und `token` auf der Seite speichern, um eine synchrone `GET`-Anforderung zu senden. Anschließend wird die resultierende Liste der Videos verwendet und im `videoList`-Textbereich gespeichert, den Sie auf der Seite eingerichtet haben.

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
   >Der `clientApiEndPoint` und das Token werden aus [Erstellen eines Tokens](#create-a-token) gesammelt.

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
   const avaPlayer = document.getElementById("avaPlayer");
   ```
1. Um den Player mit den Werten zu konfigurieren, über die Sie verfügen, müssen Sie die Werte wie hier angezeigt als Objekt einrichten:
   ```javascript
   avaPlayer.configure( {
      token: document.getElementById("token").value,
      clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
      videoName: document.getElementById("videoName").value
   } );
   ```
1. Laden Sie das Video in den Player, um zu beginnen.
   ```javascript
   avaPlayer.load();
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
        const avaPlayer = document.getElementById("avaPlayer");
        avaPlayer.configure( {
            token: document.getElementById("token").value,
            clientApiEndpointUrl: document.getElementById("clientApiEndpointUrl").value,
            videoName: document.getElementById("videoName").value
        } );
        avaPlayer.load();
    }
</script>
Client API endpoint URL: <input type="text" id="clientApiEndpointUrl" /><br><br>
Token: <input type="text" id="token" /><br><br>
<button type="submit" onclick="getVideos()">Get Videos</button><br><br>
<textarea rows="20" cols="100" id="videoList"></textarea><br><br>
Video name: <input type="text" id="videoName" /><br><br>
<button type="submit" onclick="playVideo()">Play Video</button><br><br>
<ava-player width="720px" id="avaPlayer"></ava-player>
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

## <a name="play-a-video"></a>Spielen Sie ein Video ab

Nachdem Sie jetzt die Seite gehostet haben, wechseln Sie dorthin und führen Sie die Schritte aus, um ein Video wiederzugeben.

1. Geben Sie die Werte **Endpunkt-URL der Client-API** und **Token** ein.
1. Wählen Sie **Videos abrufen** aus.
1. Wählen Sie in der Videoliste einen Videonamen aus, und geben Sie ihn in das Feld **Videoname** ein.
1. Wählen Sie **Video wiedergeben** aus.

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

| Name   | type             | BESCHREIBUNG                         |
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
import { Player } from '@video-analyzer/widgets';
```

Wenn Sie ein Player-Widget dynamisch erstellen möchten, können Sie Folgendes für JavaScript verwenden:
```javascript
<script async type="module" src="https://unpkg.com/@azure/video-analyzer-widgets@latest/dist/global.min.js"></script>
```

Wenn Sie diese Methode zum Importieren verwenden, müssen Sie das Player-Objekt programmgesteuert erstellen, nachdem der Import abgeschlossen ist. Im vorherigen Beispiel haben Sie das Modul mithilfe des `ava-player`-HTML-Tags zur Seite hinzugefügt. Um ein Playerobjekt mithilfe von Code zu erstellen, können Sie in JavaScript wie folgt vorgehen:

```javascript
const avaPlayer = new ava.widgets.player();
```

Oder in TypeScript:

```typescript
const avaPlayer = new Player();
```

Anschließend müssen Sie es dem HTML-Code hinzufügen:

```javascript
document.firstElementChild.appendChild(avaPlayer);
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur [Widget-API](https://github.com/Azure/video-analyzer/tree/main/widgets).
