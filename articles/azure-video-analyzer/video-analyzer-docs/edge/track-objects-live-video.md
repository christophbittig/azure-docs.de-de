---
title: Nachverfolgen von Objekten in einem Livevideo mit Azure Video Analyzer
description: In dieser Schnellstartanleitung wird veranschaulicht, wie Sie das Azure Video Analyzer-Edgemodul verwenden, um Objekte in einem Livevideofeed einer (simulierten) IP-Kamera nachzuverfolgen. Es wird gezeigt, wie Sie ein Modell für maschinelles Sehen anwenden, um Objekte in einer Teilmenge der Frames im Livevideofeed zu erkennen. Anschließend können Sie einen Knoten für die Objektnachverfolgung verwenden, um diese Objekte in den anderen Frames zu verfolgen.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b9286dc6dba80de841969af285ab57e587f560a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319261"
---
# <a name="quickstart-track-objects-in-a-live-video"></a>Schnellstart: Nachverfolgen von Objekten in einem Livevideo

[!INCLUDE [header](includes/edge-env.md)]

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie das Azure Video Analyzer-Edgemodul verwenden, um Objekte in einem Livevideofeed einer (simulierten) IP-Kamera nachzuverfolgen. Es wird gezeigt, wie Sie ein Modell für maschinelles Sehen anwenden, um Objekte in einer Teilmenge der Frames im Livevideofeed zu erkennen. Anschließend können Sie einen Knoten für die Objektnachverfolgung verwenden, um diese Objekte in den anderen Frames zu verfolgen.

Die Objektnachverfolgung ist hilfreich, wenn Sie Objekte in jedem Frame erkennen müssen, aber das Edgegerät nicht über die erforderliche Rechenleistung verfügt, um das Vision-Modell auf jeden Frame anzuwenden. Wenn der Livevideofeed beispielsweise auf 30 Frames pro Sekunde festgelegt ist und Sie Ihr Modell für maschinelles Sehen nur bei jedem 15. Frame ausführen können, verwendet die Objektnachverfolgung die Ergebnisse eines Frames und dann das [optische Durchflussverfahren](https://en.wikipedia.org/wiki/Optical_flow), um Ergebnisse für den 2., 3. usw. bis zum 14. Frame zu erstellen, bis das Modell wieder auf den nächsten Frame angewendet wird.

Für diese Schnellstartanleitung werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/track-objects-live-video/object-tracker-topology.svg" alt-text="Nachverfolgen von Objekten in Livevideos":::

In diesem Diagramm ist der Fluss der Signale in diesem Schnellstart dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](../pipeline.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](../pipeline.md#http-extension-processor).

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Er konvertiert jeden 15. Videoframe in den angegebenen Bildtyp. Anschließend leitet er das Bild über HTTP an ein anderes Edgemodul weiter, von dem ein KI-Modell hinter einem HTTP-Endpunkt ausgeführt wird. In diesem Beispiel wird für das Edgemodul das [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3)-Modell verwendet, mit dem viele Objekttypen erkannt werden können. Der Knoten des HTTP-Erweiterungsprozessors empfängt die Erkennungsergebnisse und sendet sie zusammen mit allen Videoframes (nicht nur dem 15. Frame) an den Knoten für die [Objektnachverfolgung](../pipeline.md#object-tracker-processor). Der Knoten für die Objektnachverfolgung verwendet optische Durchflussverfahren, um das Objekt in den 14 Frames zu verfolgen, auf die das KI-Modell nicht angewendet wurde. Der Knoten für die Nachverfolgung veröffentlicht seine Ergebnisse auf dem Knoten der IoT Hub-Nachrichtensenke. Der Knoten der [IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink) sendet diese Ereignisse anschließend an den [IoT Edge-Hub](../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

> [!NOTE]
> Lesen Sie die Informationen zum Kompromiss zwischen Genauigkeit und Verarbeitungsleistung, der beim Knoten für die [Objektnachverfolgung](../pipeline.md#object-tracker-processor) eingegangen werden muss.

In diesem Schnellstart führen Sie folgende Schritte aus:

1. Einrichten Ihrer Entwicklungsumgebung
1. Bereitstellen der erforderlichen Edgemodule
1. Erstellen und Bereitstellen der Livepipeline
1. Interpretieren der Ergebnisse
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
    * Das Modul **yolov3**, von dem das YOLOV3-Modell zum Erkennen unterschiedlicher Objekte genutzt wird

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/track-objects-live-video/object-tracker-modules.png" alt-text= "List of deployed IoT Edge modules":::


## <a name="create-and-deploy-the-live-pipeline"></a>Erstellen und Bereitstellen der Livepipeline

### <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

Navigieren Sie in Visual Studio Code zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

* **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
* **operations.json**: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die ausgeführt werden.
* **Program.cs**: Das Beispiel für den Programmcode, das Folgendes ausführt:
    * Laden der App-Einstellungen
    * Aufrufen direkter Methoden, die vom Video Analyzer-Modul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.
    * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
    * Aufrufen direkter Methoden zur Bereinigung der Ressourcen

1. Bearbeiten Sie die Datei operations.json:
    
    * Ändern Sie den Link zur Pipelinetopologie:
    * "pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/object-tracking/topology.json"
    * Bearbeiten Sie unter „livePipelineSet“ den Namen der Pipelinetopologie, sodass er mit dem Wert im vorherigen Link übereinstimmt:
    * "topologyName" : "ObjectTrackingWithHttpExtension"
    * Bearbeiten Sie unter „pipelineTopologyDelete“ den Namen:
    * "name" : "ObjectTrackingWithHttpExtension"
    
Öffnen Sie die URL für die Pipelinetopologie in einem Browser, und überprüfen Sie die Einstellungen für den HTTP-Erweiterungsknoten.

```
   "samplingOptions":{
       "skipSamplesWithoutAnnotation":"false",
       "maximumSamplesPerSecond":"2"
   }
```

Hier wird `skipSamplesWithoutAnnotation` auf `false` festgelegt, da der Erweiterungsknoten alle Frames an den Downstream-Objektnachverfolgungsknoten übergeben muss – unabhängig davon, ob sie über Rückschlussergebnisse verfügen. Mit der Objektnachverfolgung können Objekte über ca. 15 Frames hinweg verfolgt werden. Ihr KI-Modell verfügt über einen maximalen FPS-Wert für die Verarbeitung. Dies ist der höchste Wert, auf den `maximumSamplesPerSecond` festgelegt werden sollte.
    
## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von operations.json beginnt mit Aufrufen der direkten Methoden `pipelineTopologyList` und `livePipelineList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.
    
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
        "topologyName": "ObjectTrackingWithHttpExtension",
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
    * Ein Aufruf von `livePipelineActivate`, mit dem die Livepipeline und der Videodatenfluss aktiviert werden
    * Ein zweiter Aufruf von `livePipelineList`, um anzuzeigen, dass sich die Livepipeline im ausgeführten Zustand befindet
1. Die Ausgabe im Terminalfenster wird angehalten, und Sie werden zum Drücken der EINGABETASTE aufgefordert, um den Vorgang fortzusetzen. Drücken Sie noch nicht die EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum Ausgabefenster. Es werden Nachrichten angezeigt, die vom Video Analyzer-Edgemodul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
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

* Bei der Meldung handelt es sich um ein Diagnoseereignis. **MediaSessionEstablished** gibt an, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Mit „subject“ in „applicationProperties“ wird angegeben, dass die Nachricht über den Knoten der RTSP-Quelle in der Livepipeline generiert wurde.
* „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
* „eventTime“ gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="object-tracking-events"></a>Ereignisse der Objektnachverfolgung

Der Knoten des HTTP-Erweiterungsprozessors sendet Frame 0, 15, 30 usw. an das Modul „yolov3“ und empfängt die Rückschlussergebnisse. Anschließend werden diese Ergebnisse und alle Videoframes an den Knoten für die Objektnachverfolgung gesendet. Angenommen, für Frame 0 wurde ein Objekt erkannt. Die Objektnachverfolgung weist diesem Objekt dann eine eindeutige `sequenceId` zu. Falls das Objekt dann in den Frames 1, 2 usw. bis 14 nachverfolgt werden kann, wird ein Ergebnis mit derselben `sequenceId` ausgegeben. Beachten Sie in den folgenden Codeausschnitten aus den Ergebnissen, wie sich `sequenceId` wiederholt, während sich die Position des Begrenzungsrahmens ändert, wenn sich das Objekt bewegt.

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

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Arbeiten Sie dieses Tutorial durch: [Erkennen der Überquerung einer virtuellen Linie durch Objekte in einem Livevideo](use-line-crossing.md).
