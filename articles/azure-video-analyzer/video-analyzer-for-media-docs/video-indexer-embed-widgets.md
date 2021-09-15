---
title: Einbetten von Widgets von Azure Video Analyzer for Media (früher Video Indexer) in Ihre Apps
titleSuffix: Azure Video Analyzer for Media
description: Erfahren Sie, wie Sie Widgets von Azure Video Analyzer for Media (früher Video Indexer) in Ihre Apps einbetten.
services: azure-video-analyzer
author: Juliako
manager: femila
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 9203c666e88ba3f050bf0acda9116c9dd8dd4421
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429296"
---
# <a name="embed-video-analyzer-for-media-widgets-in-your-apps"></a>Einbetten von Video Analyzer for Media-Widgets in Ihre Apps

In diesem Artikel wird gezeigt, wie Sie Widgets von Azure Video Analyzer for Media (früher Video Indexer) in Ihre Apps einbetten. Video Analyzer for Media unterstützt das Einbetten von drei Arten von Widgets in Ihre Apps: *Kognitive Erkenntnisse*, *Player* und *Editor*.

Ab Version 2 enthält die Basis-URL des Widgets die Region des angegebenen Kontos. Ein Konto in der Region „USA, Westen“ generiert z.B.: `https://www.videoindexer.ai/embed/insights/.../?location=westus2`.

## <a name="widget-types"></a>Typen von Widgets

### <a name="cognitive-insights-widget"></a>Widget „Kognitive Erkenntnisse“

Ein Widget vom Typ „Kognitive Erkenntnisse“ enthält alle visuellen Erkenntnisse, die für Sie aus dem Prozess der Videoindizierung extrahiert wurden. Für das Widget vom Typ „Kognitive Erkenntnisse“ werden die folgenden optionalen URL-Parameter unterstützt:

|Name|Definition|BESCHREIBUNG|
|---|---|---|
|`widgets` | Durch Komma getrennte Zeichenfolgen | Ermöglicht das Steuern der Erkenntnisse, die Sie rendern möchten.<br/>Beispiel: Mit `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` werden nur Benutzeroberflächenerkenntnisse für Personen und Stichwörter gerendert.<br/>Verfügbare Optionen: Personen, animierte Figuren, Stichwörter, Bezeichnungen, Stimmungen, Gefühle, Themen, KeyFrames, gesprochener Text, OCR, Referenten, Szenen und benannte Entitäten.|
|`controls`|Durch Komma getrennte Zeichenfolgen|Ermöglicht das Steuern der Steuerelemente, die Sie rendern möchten.<br/>Beispiel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` rendert nur die Suchoption und die Herunterladen-Schaltfläche.<br/>Verfügbare Optionen: Suchen, Herunterladen, Voreinstellungen, Sprache.|
|`language`|Ein kurzer Sprachcode (Sprachname)|Steuert die Sprache für Erkenntnisse.<br/>Beispiel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>oder `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Ein kurzer Sprachcode | Steuert die Sprache der Benutzeroberfläche. Standardwert: `en`. <br/>Beispiel: `locale=de`.|
|`tab` | Die Registerkarte, die standardmäßig ausgewählt ist | Steuert die Registerkarte **Erkenntnisse**, die standardmäßig gerendert wird. <br/>Beispiel: `tab=timeline` rendert Erkenntnisse, während die Registerkarte **Zeitachse** ausgewählt ist.|
|`location` ||Der Parameter `location` muss in den eingebetteten Links enthalten sein. Siehe hierzu [Abrufen des Namens Ihrer Region](regions.md). Wenn sich Ihr Konto in der Vorschauphase befindet, sollte `trial` als Standortwert verwendet werden. `trial` ist der Standardwert für den Parameter `location`.| 

### <a name="player-widget"></a>Player-Widget

Sie können das Player-Widget zum Streamen von Videos verwenden, indem Sie eine adaptive Bitrate verwenden. Das Player-Widget unterstützt die folgenden optionalen URL-Parameter.

|Name|Definition|BESCHREIBUNG|
|---|---|---|
|`t` | Sekunden ab Start | Aktiviert die Wiedergabe durch den Player ab dem angegebenen Zeitpunkt.<br/> Beispiel: `t=60`. |
|`captions` | Ein Sprachcode | Ruft die Beschriftungen beim Laden des Widgets in der angegebenen Sprache ab, damit sie im Menü für die **Beschriftungen** verfügbar sind.<br/> Beispiel: `captions=en-US`. |
|`showCaptions` | Ein boolescher Wert | Dient zum Laden des Players mit bereits geladenen Beschriftungen.<br/> Beispiel: `showCaptions=true`. |
|`type`| | Aktiviert ein Design für den Audioplayer (der Videoteil wird entfernt).<br/> Beispiel: `type=audio`. |
|`autoplay` | Ein boolescher Wert | Gibt an, ob der Player beim Laden mit der Wiedergabe des Videos beginnen soll. Standardwert: `true`.<br/> Beispiel: `autoplay=false`. |
|`language`/`locale` | Ein Sprachcode | Dient zum Steuern der Sprache des Players. Standardwert: `en-US`.<br/>Beispiel: `language=de-DE`.|
|`location` ||Der Parameter `location` muss in den eingebetteten Links enthalten sein. Siehe hierzu [Abrufen des Namens Ihrer Region](regions.md). Wenn sich Ihr Konto in der Vorschauphase befindet, sollte `trial` als Standortwert verwendet werden. `trial` ist der Standardwert für den Parameter `location`.| 

### <a name="editor-widget"></a>Editor-Widget

Mit dem Editor-Widget können Sie neue Projekte erstellen und Erkenntnisse aus Videos verwalten. Das Editor-Widget unterstützt die folgenden optionalen URL-Parameter.

|Name|Definition|BESCHREIBUNG|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Bietet Zugriff auf Videos, die sich nur in dem Konto befinden, das zum Einbetten des Widgets verwendet wurde.<br> Das Editor-Widget erfordert den Parameter `accessToken`. |
|`language` | Ein Sprachcode | Dient zum Steuern der Sprache des Players. Standardwert: `en-US`.<br/>Beispiel: `language=de-DE`. |
|`locale` | Ein kurzer Sprachcode | Steuert die Sprache, in der Erkenntnisse angezeigt werden. Standardwert: `en`.<br/>Beispiel: `language=de`. |
|`location` ||Der Parameter `location` muss in den eingebetteten Links enthalten sein. Siehe hierzu [Abrufen des Namens Ihrer Region](regions.md). Wenn sich Ihr Konto in der Vorschauphase befindet, sollte `trial` als Standortwert verwendet werden. `trial` ist der Standardwert für den Parameter `location`.| 

<sup>*</sup>Der Besitzer sollte beim Bereitstellen von `accessToken` Vorsicht walten lassen.

## <a name="embed-videos"></a>Einbetten von Videos

In diesem Abschnitt wird das Einbetten von Videos in Apps erläutert. Hierfür gibt es zwei Möglichkeiten: [über das Portal](#the-portal-experience) oder durch [manuelles Erstellen der URL](#assemble-the-url-manually). 

Der Parameter `location` muss in den eingebetteten Links enthalten sein. Siehe hierzu [Abrufen des Namens Ihrer Region](regions.md). Wenn sich Ihr Konto in der Vorschauphase befindet, sollte `trial` als Standortwert verwendet werden. `trial` ist der Standardwert für den Parameter `location`. Beispiel: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>Portal

Gehen Sie im Portal wie hier beschrieben vor, um ein Video einzubetten:

1. Melden Sie sich auf der [Video Analyzer for Media](https://www.videoindexer.ai/)-Website an.
1. Wählen Sie das Video aus, mit dem Sie arbeiten möchten, und drücken Sie **Wiedergabe**.
1. Wählen Sie den gewünschten Widget-Typ aus (**Kognitive Erkenntnisse**, **Player** oder **Editor**).
1. Klicken Sie auf **&lt;/&gt; Einbetten**.
5. Kopieren Sie den Einbindungscode (wird in **Einbindungscode kopieren** im Dialogfeld **Teilen und einbetten** angezeigt).
6. Fügen Sie Ihrer App den Code hinzu.

> [!NOTE]
> Die Freigabe eines Links für das **Player**- oder **Insights**-Widget umfasst das Zugriffstoken und gewährt Ihrem Konto schreibgeschützte Berechtigungen.

### <a name="assemble-the-url-manually"></a>Manuelles Erstellen der URL

#### <a name="public-videos"></a>Öffentliche Videos

Wenn Sie öffentliche Videos einbetten möchten, können Sie die URL wie folgt erstellen:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Private Videos

Wenn Sie ein privates Video einbetten möchten, müssen Sie ein Zugriffstoken übergeben. Verwenden Sie hierzu [Get Video Access Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) im Attribut `src` des iframe-Elements:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Bereitstellen von Funktionen zur Bearbeitung von Erkenntnissen

Um Funktionen zur Bearbeitung von Erkenntnissen in Ihrem eingebetteten Widget bereitzustellen, müssen Sie ein Zugriffstoken übergeben, das Berechtigungen zur Bearbeitung enthält. Verwenden Sie [Get Video Access Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) mit `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interaktion von Widgets

Das Widget „Kognitive Erkenntnisse“ kann mit einem Video in Ihrer App interagieren. In diesem Abschnitt wird veranschaulicht, wie Sie diese Interaktion erreichen.

![Cognitive Insights-Widget – Video Analyzer for Media](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Übersicht über den Ablauf

Beim Bearbeiten der Transkripte findet der folgende Flow statt:

1. Sie bearbeiten das Transkript auf der Zeitachse.
1. Video Analyzer for Media ruft diese Updates ab und speichert sie im Bereich [Aus Transkriptbearbeitungen](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) des Sprachmodells.
1. Die Beschriftungen werden aktualisiert:

    * Wenn Sie das Player-Widget von Video Analyzer for Media verwenden, wird es automatisch aktualisiert.
    * Wenn Sie einen externen Player verwenden, erhalten Sie eine neue Beschriftungsdatei über das **Abrufen von Videobeschriftungen**.

### <a name="cross-origin-communications"></a>Ursprungsübergreifende Kommunikation

Damit Video Analyzer for Media-Widgets mit anderen Komponenten kommunizieren können, geht der Video Analyzer for Media-Dienst folgendermaßen vor:

- Verwendung der HTML5-Methode `postMessage` für die ursprungsübergreifende Kommunikation.
- Überprüfung der Nachricht für den VideoIndexer.ai-Ursprung

Wenn Sie Ihren eigenen Playercode implementieren und die Integration mit Widgets vom Typ „Kognitive Erkenntnisse“ durchführen, sind Sie selbst dafür verantwortlich, den Ursprung der Nachricht von „VideoIndexer.ai“ zu überprüfen.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Einbetten von Widgets in Ihre App oder Ihren Blog (empfohlen)

In diesem Abschnitt wird veranschaulicht, wie Sie die Interaktion zwischen zwei Video Analyzer for Media-Widgets so konfigurieren, dass der Player zum relevanten Moment springt, wenn ein Benutzer in Ihrer App das Steuerelement für Erkenntnisse auswählt.

1. Kopieren Sie den Einbettungscode des Player-Widgets.
2. Kopieren Sie den Einbettungscode von „Kognitive Erkenntnisse“.
3. Fügen Sie die [Mediator-Datei](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) für die Kommunikation zwischen den beiden Widgets hinzu:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Wenn ein Benutzer in Ihrer App jetzt das Steuerelement für Erkenntnisse auswählt, springt der Player zum relevanten Moment.

Weitere Informationen finden Sie in der [Video Analyzer for Media-Demo zum Einbetten beider Widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Einbetten des Widgets „Kognitive Erkenntnisse“ und Verwenden von Azure Media Player zum Wiedergeben der Inhalte

In diesem Abschnitt wird veranschaulicht, wie Sie die Interaktion zwischen einem Widget vom Typ „Kognitive Erkenntnisse“ und einer Azure Media Player-Instanz per [AMP-Plug-In](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js) einrichten.

1. Fügen Sie ein Video Analyzer for Media-Plug-In für den AMP-Player hinzu:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instanziieren Azure Media Player mit dem Video Analyzer for Media-Plug-In.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Kopieren Sie den Einbettungscode von „Kognitive Erkenntnisse“.

Nun können Sie mit Azure Media Player kommunizieren.

Weitere Informationen finden Sie in der Demo zu [Azure Media Player und Video Indexer-Erkenntnisse](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-analyzer-for-media-cognitive-insights-widget-and-use-a-different-video-player"></a>Einbetten des Video Analyzer for Media-Widgets vom Typ „Kognitive Erkenntnisse“ und Verwendung eines anderen Videoplayers

Wenn Sie einen anderen Videoplayer als Azure Media Player verwenden, müssen Sie den Videoplayer manuell bearbeiten, um die Kommunikation zu ermöglichen.

1. Fügen Sie Ihren Videoplayer ein.

    Dies kann z. B. ein HTML5-Standardplayer sein:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Betten Sie das Widget „Kognitive Erkenntnisse“ ein.
3. Implementieren Sie die Kommunikation für Ihren Player, indem Sie auf das Ereignis „message“ lauschen. Beispiel:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videoindexer domain.
          if ((origin === "https://www.videoindexer.ai") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Weitere Informationen finden Sie in der Demo zu [Azure Media Player und Video Indexer-Erkenntnisse](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Hinzufügen von Untertiteln

Wenn Sie Video Analyzer for Media-Erkenntnisse in Ihren eigenen [Azure Media Player](https://aka.ms/azuremediaplayer) einbetten, können Sie die `GetVttUrl`-Methode verwenden, um Untertitel abzurufen. Sie können auch eine JavaScript-Methode aus dem Video Analyzer for Media-AMP-Plug-In `getSubtitlesUrl` abrufen (wie bereits gezeigt).

## <a name="customizing-embeddable-widgets"></a>Anpassen von für die Einbettung geeigneten Widgets

### <a name="cognitive-insights-widget"></a>Widget „Kognitive Erkenntnisse“

Sie können die gewünschte Art von Erkenntnissen auswählen. Geben Sie sie dazu als Wert für den folgenden URL-Parameter an, der dem abgerufenen Einbettungscode hinzugefügt wird (aus der API oder der Web-App): `&widgets=<list of wanted widgets>`.

Die möglichen Werte sind `people`, `animatedCharacters` , `keywords`, `labels`, `sentiments`, `emotions`, `topics`, `keyframes`, `transcript`, `ocr`, `speakers`, `scenes` und `namedEntities`.

Wenn Sie z. B. ein Widget einbetten möchten, das nur Erkenntnisse zu Personen und Stichwörtern enthalten soll, sieht die iframe-URL für die Einbettung wie folgt aus:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Der Titel des iframe-Fensters kann auch angepasst werden, indem für die iframe-URL `&title=<YourTitle>` angegeben wird. (Dient zum Anpassen des HTML-Werts `<title>`.)
   
Falls Sie Ihrem iframe-Fenster beispielsweise den Titel „MyInsights“ geben möchten, sieht die URL wie folgt aus:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Beachten Sie, dass diese Option nur in Fällen relevant ist, in denen Sie die Erkenntnisse in einem neuen Fenster öffnen müssen.

### <a name="player-widget"></a>Player-Widget

Wenn Sie den Video Analyzer for Media-Player einbetten, können Sie die Größe des Players wählen, indem Sie die iframe-Größe angeben.

Beispiel:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Standardmäßig verfügt der Video Analyzer for Media-Player über automatisch generierte Untertitel, die auf dem Transkript des Videos basieren. Das Transkript wird aus dem Video mit der Quellsprache extrahiert, die beim Hochladen des Videos ausgewählt wurde.

Wenn das Einbetten mit einer anderen Sprache erfolgen soll, können Sie `&captions=<Language Code>` zur Player-URL hinzufügen. Falls die Untertitel standardmäßig angezeigt werden sollen, können Sie &showCaptions=true übergeben.

Die URL für die Einbettung sieht dann wie folgt aus:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Automatische Wiedergabe

Standardmäßig beginnt der Player mit der Wiedergabe des Videos. Sie können dies verhindern, indem Sie `&autoplay=false` an die vorhergehende URL für die Einbettung übergeben.

## <a name="code-samples"></a>Codebeispiele

Navigieren Sie zum Repository mit den [Codebeispielen](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets), das Beispiele für die Video Analyzer for Media-API und -Widgets enthält:

| Datei/Ordner                       | BESCHREIBUNG                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Laden eines Video Analyzer for Media-Videos in einen benutzerdefinierten Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Einbetten von VI-Erkenntnissen in einen benutzerdefinierten Azure Media Player.                             |
| `control-vi-embedded-player`      | Einbetten von VI Player und dessen Steuerung von außen.                                    |
| `custom-index-location`           | Einbetten von VI-Erkenntnissen aus einem benutzerdefinierten externen Speicherort (kann ein Blob eines Kunden sein).     |
| `embed-both-insights`             | Grundlegende Verwendung von VI-Informationen, sowohl Player als auch Erkenntnisse.                            |
| `embed-insights-with-AMP`         | Einbetten eines VI-Widgets vom Typ „Kognitive Erkenntnisse“ mit einem benutzerdefinierten Azure Media Player.                      |
| `customize-the-widgets`           | Einbetten von VI-Widgets mit benutzerdefinierten Optionen.                                     |
| `embed-both-widgets`              | Einbetten von VI-Player und -Erkenntnissen und Kommunizieren zwischen den Widgets.                      |
| `url-generator`                   | Generiert benutzerdefinierte Einbindungs-URLs für Widgets auf der Grundlage der vom Benutzer angegebenen Optionen.             |
| `html5-player`                    | Einbetten von VI-Erkenntnissen in einen standardmäßigen HTML5-Videoplayer.                           |

## <a name="supported-browsers"></a>Unterstützte Browser

Weitere Informationen finden Sie unter [Unterstützte Browser](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anzeigen und Bearbeiten von Video Analyzer for Media-Erkenntnissen finden Sie in unter [Anzeigen und Bearbeiten von Video Analyzer for Media-Erkenntnissen](video-indexer-view-edit.md).

Schauen Sie sich auch [Video Analyzer für Media CodePen](https://codepen.io/videoindexer/pen/eGxebZ) an.
