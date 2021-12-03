---
title: Erkennen der Überquerung einer virtuellen Linie durch Objekte in einem Livevideo mithilfe von Azure Video Analyzer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Azure Video Analyzer verwenden, um die Überquerung einer Linie durch Objekte in einem Livevideofeed einer (simulierten) IP-Kamera zu erkennen.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 163dcc488de80a81b8ab9defdbdb7b84dfdbd302
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494594"
---
# <a name="tutorial-detect-when-objects-cross-a-virtual-line-in-a-live-video"></a>Tutorial: Erkennen der Überquerung einer virtuellen Linie durch Objekte in einem Livevideo

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial erfahren Sie, wie Sie Azure Video Analyzer verwenden, um die Überquerung einer virtuellen Linie durch Objekte in einem Livevideofeed einer (simulierten) IP-Kamera zu erkennen. Es wird gezeigt, wie Sie ein Modell für maschinelles Sehen anwenden, um Objekte in einer Teilmenge der Frames im Livevideofeed zu erkennen. Anschließend können Sie diese Objekte in den anderen Frames mithilfe eines Objektverfolgungsknotens verfolgen und die Ergebnisse an einen Linienüberquerungsknoten senden.

Der Linienüberquerungsknoten ermöglicht die Erkennung der Überquerung der virtuellen Linie durch Objekte. Die Ereignisse enthalten die Richtung (im Uhrzeigersinn, gegen den Uhrzeigersinn) und jeweils eine Gesamtanzahl pro Richtung.  

In diesem Tutorial werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-tracker-topology.png" alt-text="Erkennen der Überquerung einer virtuellen Linie durch Objekte in einem Livevideo":::

In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](../pipeline.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](../pipeline.md#http-extension-processor).

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Er konvertiert jeden zehnten Videoframe in den angegebenen Bildtyp. Anschließend leitet er das Bild über HTTP an ein anderes Edgemodul weiter, von dem ein KI-Modell hinter einem HTTP-Endpunkt ausgeführt wird. In diesem Beispiel wird dieses Edge-Modul unter Verwendung des [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3)-Modells erstellt, mit dem viele Objekttypen erkannt werden können. Der HTTP-Erweiterungsprozessorknoten sammelt die Erkennungsergebnisse und sendet sie zusammen mit allen Videoframes (nicht nur dem zehnten Frame) an den Objektverfolgungsknoten. Der Objektverfolgungsknoten verwendet optische Flusstechniken, um das Objekt in den neun Frames zu verfolgen, auf die das KI-Modell nicht angewendet wurde. Der Verfolgungsknoten veröffentlicht seine Ergebnisse im Knoten der IoT Hub-Nachrichtensenke. Der Knoten der [IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink) sendet diese Ereignisse anschließend an den [IoT Edge-Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

Der Linienüberquerungsknoten erhält die Ergebnisse vom Upstream-Objektverfolgungsknoten. Die Ausgabe des Objektverfolgungsknotens enthält die Koordinaten der erkannten Objekte. Diese Koordinaten werden vom Linienüberquerungsknoten mit den der Linienkoordinaten abgeglichen. Wenn Objekte die Linie überqueren, gibt der Linienüberquerungsknoten ein Ereignis aus. Die Ereignisse werden an die Nachrichtensenke des IoT Edge-Hubs gesendet. 

In diesem Lernprogramm lernen Sie Folgendes:

1. Einrichten Ihrer Entwicklungsumgebung
1. Bereitstellen der erforderlichen Edgemodule
1. Erstellen und Bereitstellen der Livepipeline
1. Interpretieren der Ergebnisse
1. Berechnen von Koordinaten
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>Bereitstellen der erforderlichen Module

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf die Datei *src/edge/deployment.yolov3.template.json*, und wählen Sie **IoT Edge-Bereitstellungsmanifest generieren** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Generieren des IoT Edge-Bereitstellungsmanifests":::
1. Die Manifestdatei *deployment.yolov3.amd64.json* wird im Ordner *src/edge/config* erstellt.
1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/deployment.yolov3.amd64.json*, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for Single Device":::
1. Wählen Sie **avasample-iot-edge-device** aus, wenn Sie zum Auswählen eines IoT Hub-Geräts aufgefordert werden.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

    * Das Video Analyzer-Edgemodul mit dem Namen **avaedge**
    * Das Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert 
    * Das Modul **avaextension**. Hierbei handelt es sich um das YOLOv3-Objekterkennungsmodell, das maschinelles Sehen auf Bilder anwendet und mehrere Klassen von Objekttypen zurückgibt.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "YoloV3 object detection model":::


## <a name="create-and-deploy-the-live-pipeline"></a>Erstellen und Bereitstellen der Livepipeline

### <a name="review-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen wird ein kurzes Video von Verkehr auf einer Schnellstraße auf die Linux-VM in Azure kopiert, die Sie als IoT Edge-Gerät verwenden. In diesem Tutorial wird die Videodatei verwendet, um einen Livestream zu simulieren.

Öffnen Sie eine Anwendung wie etwa den [VLC Media Player](https://www.videolan.org/vlc/). Drücken Sie STRG+N, und fügen Sie dann einen Link zum [Beispielvideo zur Schnellstraßenkreuzung](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) ein, um die Wiedergabe zu starten. Sie sehen das Videomaterial vieler Fahrzeuge, die sich im Verkehr einer Schnellstraße bewegen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

### <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

Navigieren Sie in Visual Studio Code zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

* **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
* **operations.json**: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die ausgeführt werden.
* **Program.cs**: Das Beispiel für den Programmcode, das Folgendes ausführt:
    * Laden der App-Einstellungen
    * Aufrufen direkter Methoden, die durch das Video Analyzer-Edgemodul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.
    * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
    * Aufrufen direkter Methoden zur Bereinigung der Ressourcen

1. Bearbeiten Sie die Datei operations.json:
    
    * Ändern Sie den Link zur Pipelinetopologie:
    * `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/line-crossing/topology.json"`
    * Bearbeiten Sie unter `livePipelineSet` den Namen der Topologie, damit er mit dem Wert im vorherigen Link übereinstimmt:
    * `"topologyName" : "LineCrossingWithHttpExtension"`
    * Ändern Sie unter `pipelineTopologyDelete` den Namen:
    * `"name" : "LineCrossingWithHttpExtension"`
    
Öffnen Sie die URL für die Pipelinetopologie in einem Browser, und überprüfen Sie die Einstellungen für den HTTP-Erweiterungsknoten.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

Hier wird `skipSamplesWithoutAnnotation` auf `false` festgelegt, da der Erweiterungsknoten alle Frames an den Downstream-Objektnachverfolgungsknoten übergeben muss – unabhängig davon, ob sie über Rückschlussergebnisse verfügen. Mit der Objektnachverfolgung können Objekte über ca. 15 Frames hinweg verfolgt werden. Ihr KI-Modell verfügt über einen maximalen FPS-Wert für die Verarbeitung. Dies ist der höchste Wert, auf den `maximumSamplesPerSecond` festgelegt werden sollte.

Sehen Sie sich auch die Parameterplatzhalter `linecrossingName` und `lineCoordinates` für den Linienüberquerungsknoten an. Wir haben zwar Standardwerte für diese Parameter angegeben, Sie überschreiben diese aber mithilfe der Datei „operations.json“. Sehen Sie sich an, wie andere Parameter aus der Datei „operations.json“ an eine Topologie (RTSP-URL) übergeben werden.  

 
## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von operations.json beginnt mit Aufrufen der direkten Methoden `pipelineTopologyList` und `livePipelineList`. Wenn Sie nach dem Durchlaufen vorheriger Schnellstartanleitungen/Tutorials eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und die Ausführung wird angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.
    
    ```
    -------------------------------Executing operation pipelineTopologyList-----------------------  
    Request: pipelineTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.1"
    }
    ---------------  
    Response: pipelineTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
    
    Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:
    
    * Ein Aufruf von `pipelineTopologySet`, bei dem Inhalte von `pipelineTopologyUrl` verwendet werden
    * Ein Aufruf von `livePipelineSet`, der den folgenden Textkörper verwendet:
        
    ```json
    {
    "@apiVersion": "1.1",
    "name": "Sample-Pipeline-1",
    "properties": {
      "topologyName": "LineCrossingWithHttpExtension",
      "description": "Sample pipeline description",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        },
        {
          "name": "rtspPassword",
          "value": "testpassword"
        }
      ]
     }
    }
    ```
    * Ein Aufruf von `livePipelineActivate`, mit dem die Livepipeline und der Videodatenfluss gestartet werden
    * Ein zweiter Aufruf von `livePipelineList` mit der Anzeige, dass sich die Livepipeline im ausgeführten Zustand befindet
1. Die Ausgabe im Terminalfenster wird angehalten, und Sie werden zum Drücken der EINGABETASTE aufgefordert, um den Vorgang fortzusetzen. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum Ausgabefenster. Es werden Nachrichten angezeigt, die vom Video Analyzer-Modul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieses Tutorials sind diese Meldungen beschrieben.
1. Die Livepipeline wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Livepipeline zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE.
1. Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:

    * Mit dem Aufruf von `livePipelineDeactivate` wird die Livepipeline deaktiviert.
    * Mit dem Aufruf von `livePipelineDelete` wird die Livepipeline gelöscht.
    * Mit dem Aufruf von `pipelineTopologyDelete` wird die Pipelinetopologie gelöscht.
    * Ein abschließender Aufruf von `pipelineTopologyList` zeigt, dass die Liste leer ist.
    
## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie die Livepipeline ausführen, durchlaufen die Ergebnisse vom Knoten des HTTP-Erweiterungsprozessors den Knoten der IoT Hub-Nachrichtensenke und werden an den IoT-Hub gesendet. Die im **Ausgabefenster** angezeigten Meldungen enthalten jeweils die Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden vom Video Analyzer-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Livepipeline aktiviert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp lautet **MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafetaria-01.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T17:33:09.554Z",
    "dataVersion": "1.0"
  },
  }
}
```

Beachten Sie in dieser Meldung diese Details:

* Bei der Meldung handelt es sich um ein Diagnoseereignis. **MediaSessionEstablished** gibt an, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* In `applicationProperties` wird durch „subject“ angegeben, dass die Meldung über den Knoten der RTSP-Quelle in der Livepipeline generiert wurde.
* In `applicationProperties` wird durch „eventType“ angegeben, dass es sich bei diesem Ereignis um ein Diagnoseereignis handelt.
* „eventTime“ gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="line-crossing-events"></a>Linienüberquerungsereignisse

Der HTTP-Erweiterungsprozessorknoten sendet den 0., 15., 30., ... Frame an das Modul „avaextension“ und empfängt die Rückschlussergebnisse. Anschließend werden diese Ergebnisse und alle Videoframes an den Objektverfolgungsknoten gesendet. Die Objektverfolgung generiert Rückschlussergebnisse für alle Frames (0, 1, 2, ...), die dann vom Linienüberquerungsknoten mit den in der Topologie angegebenen Linienkoordinaten abgeglichen werden. Wenn Objekte diese Koordinaten überqueren, wird ein Ereignis ausgelöst. Das Ereignis sieht wie folgt aus:
```
{
  "body": {
    "timestamp": 145865319410261,
    "inferences": [
      {
        "type": "event",
        "subtype": "lineCrossing",
        "inferenceId": "8f4f7b25d6654536908bcfe34374a15e",
        "relatedInferences": [
          "c9ea5decdd6a487089ded249c748cf5b"
        ],
        "event": {
          "name": "LineCrossing1",
          "properties": {
            "counterclockwiseTotal": "35",
            "direction": "counterclockwise",
            "total": "38",
            "clockwiseTotal": "3"
          }
        }
      }
    ]
  },
```
Beachten Sie in dieser Meldung diese Details:
* Der Typ lautet `event` und hat den Untertyp `lineCrossing`.
* Das Ereignis enthält den Namen (`name`) gemäß der Angabe in der Topologie der überquerten Linie.
* Die Gesamtanzahl von Linienüberquerungen in beliebiger Richtung (`total`).
* Die Gesamtanzahl von Überquerungen im Uhrzeigersinn (`clockwiseTotal`).
* Die Gesamtanzahl von Überquerungen gegen den Uhrzeigersinn (`counterclockwiseTotal`).
* `direction` enthält die Richtung für dieses Ereignis.

> [!NOTE] 
> Wenn Sie Azure-Ressourcen mithilfe der Bereitstellung mit nur einem Klick für dieses Tutorial bereitgestellt haben, wird ein virtueller Standard-DS1-Computer erstellt. Um jedoch genaue Ergebnisse von ressourcenintensiven KI-Modellen wie YOLO zu erhalten, müssen Sie möglicherweise die VM-Größe erhöhen. [Ändern Sie die Größe des virtuellen Computers](../../../virtual-machines/windows/resize-vm.md), um die Anzahl von vCPUs und die Arbeitsspeichergröße Ihren Anforderungen gemäß zu erhöhen. Aktivieren Sie die Livepipeline dann erneut, um Rückschlüsse zu sehen.

## <a name="customize-for-your-own-environment"></a>Anpassen für Ihre eigene Umgebung

In diesem Tutorial wird das bereitgestellte Beispielvideo verwendet, für das wir die korrekten Koordinaten der Linie berechnet haben. In der Topologiedatei sehen Sie, dass der Parameter `lineCoordinates` den folgenden Wert enthält: `[[0.5,0.1], [0.5,0.9]]`

Was bedeutet dieser Wert? Wenn Sie in einem 2D-Bild eine Linie zeichnen möchten, benötigen Sie zwei Punkte: A und B. Diese Punkte werden durch eine imaginäre Linie verbunden. Jeder Punkt hat seine eigene x- und y-Koordinate, um zu bestimmen, wo er sich in Relation zur vollständigen Bildauflösung befindet. In diesem Fall hat Punkt A die Koordinaten `[0.5,0.1]` und Punkt B die Koordinaten `[0.5,0.9]`. Eine visuelle Darstellung der Linie sieht wie folgt aus:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-visual-example.png" alt-text="Visuelles Beispiel, das ein Bild mit einer Linie zeigt":::

Auf diesem Bild sehen Sie die Linie zwischen den Punkten A und B. Durch jedes Objekt, das die Linie überquert, wird ein Ereignis mit Eigenschaften wie etwa der Richtung erstellt, wie weiter oben in diesem Tutorial erläutert. Beachten Sie auch die x- und y-Achse in der linken unteren Ecke. Sie dient lediglich zur Veranschaulichung und verdeutlicht, wie wir die Koordinaten auf die Werte normalisieren, die für den Linienüberquerungsknoten erwartet werden. 

Im Anschluss folgt eine Beispielberechnung. Angenommen, die Videoauflösung beträgt 1920 × 1080. Bei 1920 und 1080 handelt es sich um die Anzahl von Pixeln entlang der x- bzw. y-Achse.
Erstellen Sie ein Bild auf der Grundlage eines Frames des Videos, das Sie verwenden möchten. Öffnen Sie dieses Bild nun in einem Bildbearbeitungsprogramm (MSPaint). Bewegen Sie den Cursor an die Position, an der Sie Punkt A angeben möchten. Die x- und y-Koordinate für diese Cursorposition werden in der linken unteren Ecke angezeigt.
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/line-crossing-mspaint-coordinates.png" alt-text="Visuelles Beispiel für die Linienüberquerung mit MSPaint":::

Notieren Sie sich diese Werte. Führen Sie anschließend die gleichen Schritte für Punkt B aus, und notieren Sie sich ebenfalls die entsprechenden Werte. Nun sollten Sie über den x- und y-Wert für Punkt A sowie über den x- und y-Wert für Punkt B verfügen. (Beispiel: Punkt A: x = 1024, y = 96; Punkt B: x = 1024, y = 960) Diese Werte sehen nicht wie Werte für den Linienüberquerungsknoten aus, da hier Werte zwischen 0 und 1 benötigt werden. Für die entsprechende Berechnung wird die folgende Formel verwendet:

`x coordinate / image resolution along x axis`. In unserem Beispiel also: `1024/1920 = 0.5`. Gehen Sie für den y-Wert auf die gleiche Weise vor: `96/1080=0.1`. Dies sind die normalisierten Koordinaten für Punkt A. Wiederholen Sie diese Schritte für Punkt B. Wie weiter oben in diesem Tutorial gezeigt, erhalten Sie ein Array von Werten zwischen 0 und 1: `[[0.5,0.1], [0.5,0.9]]`.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Testen Sie die Livepipeline mit verschiedenen Videos.
