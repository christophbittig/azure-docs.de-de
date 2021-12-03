---
title: Aufzeichnen und Streamen von Rückschlussmetadaten mit Video – Azure Video Analyzer
description: In diesem Tutorial erfahren Sie, wie Sie Azure Video Analyzer verwenden, um Video- und Rückschlussmetadaten in der Cloud aufzuzeichnen und die Aufzeichnung mit den visuellen Rückschlussmetadaten wiederzugeben.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 67331e8b39a7c059da31215dd971988009a0ee36
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491979"
---
# <a name="tutorial-record-and-stream-inference-metadata-with-video"></a>Tutorial: Aufzeichnen und Streamen von Rückschlussmetadaten mit Video

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial erfahren Sie, wie Sie Azure Video Analyzer verwenden, um Livevideo- und Rückschlussmetadaten in der Cloud aufzuzeichnen und die Aufzeichnung mit den visuellen Rückschlussmetadaten wiederzugeben. In diesem Anwendungsfall zeichnen Sie kontinuierlich Videos auf, während Sie ein benutzerdefiniertes Modell zum Erkennen von Objekten **(yoloV3)** und einen Video Analyzer-Prozessor **(Objektnachverfolgung)** zum Nachverfolgen von Objekten verwenden. Während das Video kontinuierlich aufgezeichnet wird, werden auch die Rückschlussmetadaten aus den erkannten und nachverfolgten Objekten erfasst. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Azure Video Analyzer in IoT Edge: Übersicht](../overview.md)
* [Azure Video Analyzer in IoT Edge: Terminologie](../terminology.md)
* [Video Analyzer-Pipelinekonzepte](../pipeline.md) 
* [Fortlaufende Videoaufzeichnung](../continuous-video-recording.md)
* [Schnellstart: Analysieren von Livevideos mit einem eigenen Modell - HTTP](analyze-live-video-use-your-model-http.md)
* [Schnellstart: Nachverfolgen von Objekten in einem Livevideo](track-objects-live-video.md)

## <a name="prerequisites"></a>Voraussetzungen

Dies sind die Voraussetzungen für dieses Tutorial: [!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/record-stream-inference-data-with-video/overview.svg" alt-text="Pipeline":::

Das Diagramm ist die bildliche Darstellung einer [Pipeline](../pipeline.md) und weiterer Module, mit denen das gewünschte Szenario realisiert wird. Drei IoT Edge-Module sind beteiligt:
* Video Analyzer-Modul.
* Ein Edge-Modul, das ein KI-Modell hinter einem HTTP-Endpunkt ausführt. Dieses KI-Modul verwendet das [YOLO v3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3)-Modell, das viele Objekttypen erkennen kann.
* Ein [RTSP-Simulatormodul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) zum Simulieren einer RTSP-Kamera.

Wie das Diagramm zeigt, verwenden Sie einen [RTSP-Quellknoten](../pipeline.md#rtsp-source) in der Pipeline zum Erfassen des simulierten Livevideos von Verkehr auf einer Schnellstraße und senden dieses Video an zwei Pfade:

* Der erste Pfad führt zu einem HTTP-Erweiterungsknoten. Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Er konvertiert jeden zehnten Videoframe in den angegebenen Bildtyp. Dann leitet es das Bild über HTTP an ein anderes Edge-Modul weiter, das ein KI-Modell hinter einem HTTP-Endpunkt ausführt. In diesem Beispiel wird dieses Edge-Modul unter Verwendung des YOLOv3-Modells erstellt, mit dem viele Objekttypen erkannt werden können. Der HTTP-Erweiterungsprozessorknoten sammelt die Erkennungsergebnisse und sendet sie zusammen mit allen Videoframes (nicht nur dem zehnten Frame) an den Objektverfolgungsknoten. Der Objektverfolgungsknoten verwendet optische Flusstechniken, um das Objekt in den neun Frames zu verfolgen, auf die das KI-Modell nicht angewendet wurde. Der Verfolgungsknoten veröffentlicht seine Ergebnisse auf dem Videosenkenknoten und dem IoT Hub-Senkenknoten. Der Knoten der [Videosenke](../pipeline.md#video-sink) verwendet die Rückschlussmetadaten aus dem Objektverfolgungsknoten, die mit dem aufgezeichneten Video wiedergegeben werden. Der Knoten der [IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink) sendet diese Ereignisse anschließend an den [IoT Edge-Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

* Der zweite Pfad verläuft direkt von der RTSP-Quelle zum Knoten der Videosenke, um eine kontinuierliche Videoaufzeichnung zu erreichen. In diesem Tutorial wird ein [Beispielvideo zur Schnellstraßenkreuzung](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) verwendet.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LTY4]

In diesem Lernprogramm lernen Sie Folgendes:

1. Einrichten Ihrer Entwicklungsumgebung
1. Bereitstellen der erforderlichen Edgemodule
1. Erstellen und Bereitstellen der Livepipeline
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="examine-the-sample-files"></a>Untersuchen der Beispieldateien

Navigieren Sie in Visual Studio Code zu „src/edge“. Hier befinden sich die erstellte ENV-Datei sowie einige Bereitstellungsvorlagendateien. Durch diese Vorlage wird definiert, welche Edge-Module auf dem Edge-Gerät (dem virtuellen Azure-Linux-Computer) bereitgestellt werden. Die ENV-Datei enthält Werte für die Variablen, die in diesen Vorlagen verwendet werden, z. B. die Anmeldeinformationen für Video Analyzer.

Öffnen Sie „src/edge/deployment.yolov3.template.json“. Im Abschnitt **Module** gibt es drei Einträge, die den oben im Abschnitt „Übersicht“ aufgelisteten Elementen entsprechen.

* **avaedge**: Dies ist das Video Analyzer in IoT Edge-Modul.
* **yolov3**: Dies ist das KI-Modul, das mithilfe des YOLO v3-Modells erstellt wurde.
* **rtspsim**: Dies ist der RTSP-Simulator.


Navigieren Sie als Nächstes zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

* **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
* **operations.json**: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die ausgeführt werden.
* **Program.cs**: Das Beispiel für den Programmcode, das Folgendes ausführt:
    * Laden der App-Einstellungen

    * Aufrufen direkter Methoden, die vom Modul „Video Analyzer in IoT Edge“ verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.

    * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
    * Aufrufen direkter Methoden zur Bereinigung der Ressourcen

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests 

1. Klicken Sie mit der rechten Maustaste auf die Datei _src/edge/deployment.yolov3.template.json_, und wählen Sie dann **IoT Edge-Bereitstellungsmanifest generieren** aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Screenshot: IoT Edge-Bereitstellungsmanifest generieren":::

    * Die Manifestdatei _deployment.yolov3.amd64.json_ wird im Ordner _src/edge/config_ erstellt.
1. Klicken Sie mit der rechten Maustaste auf _src/edge/config/deployment.yolov3.amd64.json_, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Screenshot of Create Deployment for Single Device":::

1. Wählen Sie **avasample-iot-edge-device** aus, wenn Sie zum Auswählen eines IoT Hub-Geräts aufgefordert werden.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

   - Das **avaedge**-Modul, bei dem es sich um das Video Analyzer-Modul handelt.
   - Das Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert
   - Das Modul **yolov3**, bei dem es sich um das YoloV3-Objekterkennungsmodell handelt, das maschinelles Sehen auf die Bilder anwendet und mehrere Klassen von Objekttypen zurückgibt.

     > [!div class="mx-imgBorder"]
     > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "Screenshot of YoloV3 object detection model":::
     
## <a name="create-and-deploy-the-live-pipeline"></a>Erstellen und Bereitstellen der Livepipeline

### <a name="edit-the-sample-files"></a>Bearbeiten der Beispieldateien

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Navigieren Sie zu „src/cloud-to-device-console-app/operations.json“.
1. Bearbeiten Sie unter dem Knoten **pipelineTopologySet** Folgendes:
 
    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json" `
    
1. Vergewissern Sie sich anschließend unter den Knoten **livePipelineSet** und **pipelineTopologyDelete**, dass der Wert von **topologyName** dem Wert der Eigenschaft **name** in der obigen Pipelinetopologie entspricht:

    `"pipelineTopologyName" : "CVRHttpExtensionObjectTracking"`
1. Öffnen Sie die [Pipelinetopologie](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/cvr-with-httpExtension-and-objectTracking/topology.json) in einem Browser, und sehen Sie sich die Einstellung von „videoName“ an. Sie ist hartcodiert und auf `sample-cvr-with-inference-metadata` festgelegt. Für ein Tutorial ist dies akzeptabel. In der Produktion sollten Sie sicherstellen, dass die Aufzeichnung für jede RTSP-Kamera über eine Videoressource mit einem eindeutigen Namen erfolgt.  

1. Überprüfen Sie die Einstellungen für den HTTP-Erweiterungsknoten.

  ```=
     "samplingOptions":{
         "skipSamplesWithoutAnnotation":"false",
         "maximumSamplesPerSecond":"2"
    }
  ```

Hier wird `skipSamplesWithoutAnnotation` auf `false` festgelegt, da der Erweiterungsknoten alle Frames an den Downstream-Objektverfolgungsknoten übergeben muss – unabhängig davon, ob sie über Rückschlussergebnisse verfügen. Mit der Objektnachverfolgung können Objekte über ca. 15 Frames hinweg verfolgt werden. Ihr KI-Modell verfügt über einen maximalen FPS-Wert für die Verarbeitung. Dies ist der höchste Wert, auf den `maximumSamplesPerSecond` festgelegt werden sollte.


## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

1. Starten Sie eine Debugsitzung, indem Sie F5 drücken. Daraufhin werden im **Terminalfenster** einige Nachrichten ausgegeben.
1. Die Datei „operations.json“ beginnt mit Aufrufen von „pipelineTopologyList“ und „livePipelineList“. Falls Sie nach Abschluss vorheriger Schnellstartanleitungen oder Tutorials eine Ressourcenbereinigung durchgeführt haben, gibt diese Aktion leere Listen zurück, und die Ausführung wird angehalten, bis Sie die **EINGABETASTE** drücken, wie hier zu sehen:
    ```
    --------------------------------------------------------------------------
    Executing operation pipelineTopologyList
    -----------------------  Request: pipelineTopologyList  --------------------------------------------------
    {
      "@apiVersion": "1.1"
    }
    ---------------  Response: pipelineTopologyList - Status: 200  ---------------
    {
      "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    
    Press Enter to continue
    ```
1. Nachdem Sie im **Terminalfenster** die **EINGABETASTE** gedrückt haben, werden die nächsten Aufrufe direkter Methoden durchgeführt:
   * Ein Aufruf von „pipelineTopologySet“ mithilfe der vorherigen „pipelinetopologyUrl“
   * Ein Aufruf von „livePipelineSet“ mithilfe des folgenden Texts
     
        ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "CVRHttpExtensionObjectTracking",
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
   * Ein Aufruf von „livePipelineActivate“, mit dem die Livepipeline und der Videodatenfluss gestartet werden
   * Ein zweiter Aufruf von „livePipelineList“, um anzuzeigen, dass sich die Livepipeline im ausgeführten Zustand befindet
1. Die Ausgabe im Terminalfenster wird angehalten, und Sie werden zum Drücken der EINGABETASTE aufgefordert, um den Vorgang fortzusetzen. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum Ausgabefenster. Es werden Nachrichten angezeigt, die vom „Video Analyzer in IoT Edge“-Modul an den IoT-Hub gesendet werden.
1. Die Pipeline wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Livepipeline zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE.
1. Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:

    * Ein Aufruf von „livePipelineDeactivate“ deaktiviert die Livepipeline.
    * Ein Aufruf von „livePipelineDelete“ löscht die Livepipeline.
    * Ein Aufruf von „pipelineTopologyDelete“ löscht die Pipelinetopologie.
    * Durch einen abschließenden Aufruf von „pipelineTopologyList“ wird gezeigt, dass die Liste leer ist.       

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie die Livepipeline ausführen, durchlaufen die Ergebnisse vom HTTP-Erweiterungsprozessorknoten den Objektverfolgungsknoten zum IoT Hub-Senkenknoten und werden an den IoT-Hub gesendet. Die im **Ausgabefenster** angezeigten Nachrichten enthalten jeweils den Abschnitt „body“ und den Abschnitt „applicationProperties“. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden vom Video Analyzer-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

## <a name="diagnostics-events"></a>Diagnoseereignisse
### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Livepipeline aktiviert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp lautet „Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished“.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
{  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

Beachten Sie in dieser Meldung diese Details:

* Bei der Meldung handelt es sich um ein Diagnoseereignis. „MediaSessionEstablished“ gibt an, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Mit „subject“ in „applicationProperties“ wird angegeben, dass die Nachricht über den Knoten der RTSP-Quelle in der Livepipeline generiert wurde.
* „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
* „eventTime“ gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Betriebsereignisse

### <a name="object-tracking-events"></a>Ereignisse der Objektnachverfolgung

Der Knoten des HTTP-Erweiterungsprozessors sendet Frame 0, 15, 30 usw. an das Modul „yolov3“ und empfängt die Rückschlussergebnisse. Anschließend werden diese Ergebnisse und alle Videoframes an den Knoten für die Objektnachverfolgung gesendet. Angenommen, für Frame 0 wurde ein Objekt erkannt. Die Objektnachverfolgung weist diesem Objekt dann eine eindeutige „sequenceId“ zu. Falls das Objekt dann in den Frames 1, 2, usw. bis 14 nachverfolgt werden kann, wird ein Ergebnis mit derselben „sequenceId“ ausgegeben. Beachten Sie in den folgenden Codeausschnitten aus den Ergebnissen, wie sich `sequenceId` wiederholt, während sich die Position des Begrenzungsrahmens ändert, wenn sich das Objekt bewegt.

Aus Frame M:

```json
  {
    "type": "entity",
    "subtype": "objectDetection",
    "inferenceId": "4d325fc4dc7a43b2a781bf7d6bdb3ff0",
    "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
    "entity": {
      "tag": {
        "value": "car",
        "confidence": 0.95237225
      },
      "box": {
        "l": 0.0025893003,
        "t": 0.550063,
        "w": 0.1086607,
        "h": 0.12116724
      }
    }
  },
```

Aus Frame N:

```json
{
  "type": "entity",
  "subtype": "objectDetection",
  "inferenceId": "317aafdab7e940388be1e4c4cc58c366",
  "sequenceId": "0999a1dde5b241c3a0b2db025f87ab32",
  "entity": {
    "tag": {
      "value": "car",
      "confidence": 0.95237225
    },
    "box": {
      "l": 0.0027777778,
      "t": 0.54901963,
      "w": 0.108333334,
      "h": 0.12009804
    }
  }
},
```

### <a name="recordingstarted-event"></a>RecordingStarted-Ereignis
Wenn der Videosenke-Knoten mit dem Aufzeichnen der Mediendaten beginnt, wird das folgende Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** ausgegeben:

```
[IoTHubMonitor] [9:42:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde.

Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name der Video Analyzer-Ressource, auf der Videodaten aufgezeichnet werden.

### <a name="recordingavailable-event"></a>RecordingAvailable-Ereignis

Wie der Name bereits vermuten lässt, wird das Ereignis „RecordingStarted“ gesendet, wenn die Aufzeichnung begonnen hat. Unter Umständen wurden aber noch keine Mediendaten auf die Videoressource hochgeladen. Nachdem vom Videosenke-Knoten Mediendaten hochgeladen wurden, wird ein Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** ausgegeben:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass genügend Daten auf die Videoressource geschrieben wurden, um die Wiedergabe des Videos über Player oder Clients zu starten.

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde.

Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name der Video Analyzer-Ressource, auf der Videodaten aufgezeichnet werden.

### <a name="recordingstopped-event"></a>Ereignis „RecordingStopped“

Wenn Sie die Livepipeline deaktivieren, beendet der Videosenke-Knoten die Aufzeichnung von Medien. Das folgende Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** wird ausgegeben:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [ava-sample-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-cvr-with-inference-metadata"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass die Aufzeichnung beendet wurde.

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde.

Der Textabschnitt enthält Informationen zum Ausgabeort. In diesem Fall ist dies der Name der Video Analyzer-Ressource, auf der die Videodaten aufgezeichnet werden.

## <a name="streaming-the-recording-with-visual-inference-metadata"></a>Streamen der Aufzeichnung mit visuellen Rückschlussmetadaten

Sie können von der Livepipeline erstellte Video Analyzer-Ressource für Videodaten untersuchen, indem Sie sich beim Azure-Portal anmelden und das Video anzeigen.

1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie unter den Ressourcen Ihres Abonnements nach Ihrem Video Analyzer-Konto, und öffnen Sie den Kontobereich.
1. Wählen Sie in der Liste **Video Analyzers** die Option **Videos** aus.
1. Ein Video mit dem Namen `sample-cvr-with-inference-metadata` ist aufgeführt. Dies ist der Name, den Sie in Ihrer Datei mit der Pipelinetopologie ausgewählt haben.
1. Wählen Sie das Video aus.
1. Klicken Sie auf der Seite mit den Videodetails auf das **Wiedergabesymbol**.
1. Klicken Sie auf das **Begrenzungsrahmensymbol**, um die Rückschlussmetadaten als Begrenzungsrahmen im Video anzuzeigen (rot markiert).
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/video-playback.png" alt-text="Screenshot: Videowiedergabe":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras mit RTSP-Unterstützung finden Sie auf der [Seite für ONVIF-konforme Produkte](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die den Profilen G, S oder T entsprechen.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät (anstelle eines virtuellen Azure-Linux-Computers). Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md). Befolgen Sie anschließend die Anweisungen in der Schnellstartanleitung [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md), um das Gerät für Azure IoT Hub zu registrieren.
 
