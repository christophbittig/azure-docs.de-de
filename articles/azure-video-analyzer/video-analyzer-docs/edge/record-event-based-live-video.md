---
title: 'Tutorial: Ereignisbasierte Videoaufzeichnung in der Cloud und Wiedergabe aus der Cloud: Azure'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Video Analyzer verwenden, um eine ereignisbasierte Videoaufzeichnung in der Cloud durchzuführen und sie aus der Cloud wiederzugeben.
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1edec79cd7baa20819e0599b039fbdfe20f35d92
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484805"
---
# <a name="tutorial-event-based-video-recording-and-playback"></a>Tutorial: Ereignisbasierte Videoaufzeichnung und -wiedergabe

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial erfahren Sie, wie Sie mit Azure Video Analyzer Teile einer Livevideoquelle selektiv in Video Analyzer in der Cloud aufzuzeichnen. Dieser Anwendungsfall wird in diesem Tutorial als [ereignisbasierte Videoaufzeichnung](../event-based-video-recording-concept.md) (Event Based Video Recording, EVR) bezeichnet. Um Teile eines Livevideos aufzuzeichnen, verwenden Sie ein KI-Modell zur Objekterkennung, um im Videosignal nach Objekten zu suchen und Videoclips nur aufzuzeichnen, wenn ein bestimmter Objekttyp erkannt wird. Außerdem erfahren Sie mehr über die Wiedergabe der aufgezeichneten Videoclips mithilfe von Video Analyzer. Diese Funktionalität ist für eine Vielzahl von Szenarien nützlich, bei denen die Notwendigkeit besteht, ein Archiv interessierender Videoclips zu unterhalten. 

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

Lesen Sie diese Artikel, bevor Sie beginnen:

* [Azure Video Analyzer-Übersicht](../overview.md)
* [Azure Video Analyzer-Begriffe](../terminology.md)
* [Video Analyzer-Pipelinekonzepte](../pipeline.md) 
* [Ereignisbasierte Videoaufzeichnung](../event-based-video-recording-concept.md)
* [Tutorial: Entwickeln eines IoT Edge-Moduls](../../../iot-edge/tutorial-develop-for-linux.md)
* [Vorgehensweise beim Bearbeiten von „deployment.*.template.json“](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Abschnitt zum [Deklarieren von Routen im IoT Edge-Bereitstellungsmanifest](../../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Voraussetzungen

Dies sind die Voraussetzungen für dieses Tutorial:
* Ein Azure-Konto das ein aktives Abonnement beinhaltet. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keines besitzen.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Docker-Installation](https://docs.docker.com/desktop/#download-and-install) auf Ihrem Gerät
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick

Ereignisbasierte Videoaufzeichnung bezeichnet den Vorgang des Aufzeichnen von Videos mit Auslösung durch ein Ereignis. Dieses Ereignis kann in folgender Weise generiert werden:
- Verarbeitung des Videosignals selbst, beispielsweise beim Erkennen eines bewegten Objekts im Video.
- Aus einer unabhängigen Quelle, z. B. dem Öffnen einer Tür. 

Alternativ können Sie die Aufzeichnung nur dann auslösen, wenn ein Rückschlüsse ziehender Dienst erkennt, dass ein bestimmtes Ereignis eingetreten ist. In diesem Tutorial verwenden Sie ein Video von Fahrzeugen, die sich auf einer Schnellstraße bewegen, und zeichnen immer dann einen Videoclip auf, wenn ein Lastwagen erkannt wird.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/overview.svg" alt-text="Pipeline":::

Das Diagramm ist die bildliche Darstellung einer [Pipeline](../pipeline.md) und weiterer Module, mit denen das gewünschte Szenario realisiert wird. Vier IoT Edge-Module sind beteiligt:

* Video Analyzer in einem IoT Edge-Modul.
* Ein Edge-Modul, das ein KI-Modell hinter einem HTTP-Endpunkt ausführt. Dieses KI-Modul verwendet das [YOLO v3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3)-Modell, das viele Objekttypen erkennen kann.
* Ein benutzerdefiniertes Modul zum Zählen und Filtern von Objekten, das im Diagramm als Objektzähler bezeichnet wird. Sie erstellen einen Objektzähler und stellen ihn in diesem Tutorial bereit.
* Ein [RTSP-Simulatormodul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) zum Simulieren einer RTSP-Kamera.
    
Wie das Diagramm zeigt, verwenden Sie einen [RTSP-Quellknoten](../pipeline.md#rtsp-source) in der Pipeline zum Erfassen des simulierten Livevideos von Verkehr auf einer Schnellstraße und senden dieses Video an zwei Pfade:

* Der erste Pfad führt zu einem HTTP-Erweiterungsknoten. Der Knoten erfasst die Videoframes in einem von Ihnen mithilfe des Felds `samplingOptions` festgelegten Wert und leitet die Frames dann als Bilder an das KI-Modul YOLOv3 weiter, das eine Objekterkennung darstellt. Der Knoten empfängt die Ergebnisse, nämlich die vom Modell erkannten Objekte (Fahrzeuge im Verkehrsfluss). Der HTTP-Erweiterungsknoten veröffentlicht anschließend die Ergebnisse über den Knoten der IoT Hub-Nachrichtensenke auf dem IoT Edge Hub.

* Das Objektzählermodul ist so eingerichtet, dass es Nachrichten vom IoT Edge Hub empfängt – welche die Ergebnisse der Objekterkennung (Fahrzeuge im Verkehrsfluss) beinhalten. Das Modul überprüft diese Nachrichten und sucht dabei nach Objekten eines bestimmten Typs, der mithilfe einer Einstellung konfiguriert wurde. Wenn ein solches Objekt gefunden wird, sendet dieses Modul eine Nachricht an den IoT Edge Hub. Diese „Objekt gefunden“-Meldungen werden dann an den IoT Hub-Quellknoten der Pipeline weitergeleitet. Beim Empfang einer solchen Nachricht löst der IoT Hub-Quellknoten in der Pipeline den [Signalgateprozessor](../pipeline.md#signal-gate-processor)-Knoten aus. Der Signalgate-Prozessorknoten wird dann für eine konfigurierte Zeitspanne geöffnet. Das Videosignal fließt für diese Zeitspanne durch das Gate zum Knoten der Videosenke. Dieser Teil des Livestreams wird dann über den Knoten der [Videosenke](../pipeline.md#video-sink) in einem [Video](../terminology.md#video) in Ihrem Video Analyzer-Konto aufgezeichnet. In diesem Tutorial wird ein [Beispielvideo zur Schnellstraßenkreuzung](https://avamedia.blob.core.windows.net/public/camera-300s.mkv) verwendet.

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

Öffnen Sie „src/edge/deployment.objectCounter.template.json“. Im Abschnitt **Module** gibt es vier Einträge, die den oben im Abschnitt „Übersicht“ aufgelisteten Elementen entsprechen.

* **avaedge**: Dies ist das Video Analyzer-Modul.
* **yolov3**: Dies ist das KI-Modul, das mithilfe des YOLO v3-Modells erstellt wurde.
* **rtspsim**: Dies ist der RTSP-Simulator.
* **objectCounter**: Dies ist das Modul, das in den Ergebnissen von yolov3 nach spezifischen Objekten sucht.

Informationen zum objectCounter-Modul finden Sie in der Zeichenfolge (${MODULES.objectCounter}), die für den Wert „image“ verwendet wird. Dies basiert auf dem [Tutorial](../../../iot-edge/tutorial-develop-for-linux.md) zum Entwickeln eines IoT Edge-Moduls. Visual Studio Code erkennt automatisch, dass der Code für das objectCounter-Modul sich unter „src/edge/modules/objectCounter“ befindet. 

Lesen Sie [diesen Abschnitt](../../../iot-edge/module-composition.md#declare-routes) zum Deklarieren von Routen im IoT Edge-Bereitstellungsmanifest. Überprüfen Sie dann die Routen in der JSON-Vorlagendatei. Beachten Sie Folgendes:

> [!NOTE]
> Überprüfen Sie die gewünschten Eigenschaften für das objectCounter-Modul, die dafür eingerichtet sind, nach Objekten zu suchen, die mit einem Konfidenzgrad von mindestens 50 % als „LKW“ gekennzeichnet sind.

Navigieren Sie als Nächstes zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

* **c2d-console-app.csproj**: Die Projektdatei für Visual Studio Code.
* **operations.json**: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die ausgeführt werden.
* **Program.cs**: Das Beispiel für den Programmcode, das Folgendes ausführt:
    * Laden der App-Einstellungen

    * Aufrufen direkter Methoden, die durch das Video Analyzer-Modul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.

    * Anhalten der Ausführung, damit Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
    * Aufrufen direkter Methoden zur Bereinigung der Ressourcen

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests 

Im Bereitstellungsmanifest werden die Module, die auf einem Edge-Gerät bereitgestellt werden, sowie die Konfigurationseinstellungen für diese Module definiert. Führen Sie die folgenden Schritte aus, um ein Manifest auf der Grundlage der Vorlagendatei zu generieren, und stellen Sie es dann auf dem Edge-Gerät bereit.

Führen Sie in Visual Studio Code [diese Anweisungen](../../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) aus, um sich bei Docker anzumelden. Wählen Sie dann **Erstellen und Übertragen der IoT Edge-Projektmappe per Push** aus. Verwenden Sie für diesen Schritt „src/edge/deployment.objectCounter.template.json“.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/build-push.png" alt-text="Erstellen und Übertragen der IoT Edge-Projektmappe per Push":::

Durch diese Aktion wird das objectCounter-Modul zur Objektzählung erstellt und das Image per Push an Ihre Azure Container Registry (ACR) überstellt.

* Überprüfen Sie, ob die Umgebungsvariablen CONTAINER_REGISTRY_USERNAME_myacr und CONTAINER_REGISTRY_PASSWORD_myacr in der ENV-Datei definiert sind.

Mit diesem Schritt wird das IoT Edge-Bereitstellungsmanifest unter „src/edge/config/deployment.objectCounter.amd64.json“ erstellt. Klicken Sie mit der rechten Maustaste auf diese Datei, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/create-deployment-single-device.png" alt-text="Bereitstellung für einzelnes Gerät erstellen":::

Wenn dies Ihr erstes Tutorial mit Video Analyzer ist, werden Sie von Visual Studio Code aufgefordert, die IoT Hub-Verbindungszeichenfolge einzugeben. Sie können sie aus der Datei „appsettings.json“ kopieren.

[!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

Als Nächstes bittet Visual Studio Code Sie, ein IoT Hub-Gerät auszuwählen. Wählen Sie Ihr IoT Edge-Gerät aus; dies sollte „avasample-iot-edge-device“ sein.

In dieser Phase hat die Bereitstellung von Edge-Modulen auf Ihrem IoT Edge-Gerät begonnen.
Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich in Visual Studio Code. Sie sollten sechs bereitgestellte Module mit den Namen „$edgeAgent“, „$edgeHub“, „avaedge“, „rtspsim“, „yolov3“ und „objectCounter“ sehen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording/modules.png" alt-text="Vier bereitgestellte Module":::

## <a name="run-the-program"></a>Ausführen des Programms

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Navigieren Sie zu „src/cloud-to-device-console-app/operations.json“.
1. Bearbeiten Sie unter dem Knoten **pipelineTopologySet** Folgendes:


    `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json" `
    
1. Vergewissern Sie sich anschließend unter den Knoten **livePipelineSet** und **pipelineTopologyDelete**, dass der Wert von **topologyName** dem Wert der Eigenschaft **name** in der obigen Pipelinetopologie entspricht:

    `"pipelineTopologyName" : "EVRtoVideoSinkOnObjDetect"`
1. Öffnen Sie die [Pipelinetopologie](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) in einem Browser, und sehen Sie sich die Einstellung von „videoName“ an. Sie ist hartcodiert und auf `sample-evr-video` festgelegt. Für ein Tutorial ist dies akzeptabel. In der Produktion sollten Sie sicherstellen, dass die Aufzeichnung für jede RTSP-Kamera über eine Videoressource mit einem eindeutigen Namen erfolgt.
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
            "topologyName": "EVRtoVideoSinkOnObjDetect",
            "description": "Sample topology description",
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
    
   * Ein Aufruf von „livePipelineActivate“ zum Starten der Livepipeline und zum Starten des Videodatenflusses
   * Ein zweiter Aufruf von „livePipelineList“, um anzuzeigen, dass sich die Livepipeline im ausgeführten Zustand befindet 
     
1. Die Ausgabe im **Terminalfenster** wird angehalten, und Sie werden zum **Drücken der EINGABETASTE** aufgefordert, um den Vorgang fortzusetzen. Drücken Sie noch nicht die **EINGABETASTE**. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wenn Sie nun zum **Ausgabefenster** in Visual Studio Code wechseln, werden die Nachrichten angezeigt, die vom Video Analyzer-Modul an IoT Hub gesendet werden.

   Diese Nachrichten werden im folgenden Abschnitt beschrieben.
1. Die Livepipeline wird weiterhin ausgeführt, und das Video wird aufgezeichnet. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Kehren Sie zum Beenden der Aufzeichnung zum **Terminalfenster** zurück, und drücken Sie die **EINGABETASTE**. Die nächsten Aufrufe erfolgen, um die Ressourcen zu bereinigen und verwenden dazu:

   * Ein Aufruf von „livePipelineDeactivate“ zum Deaktivieren der Livepipeline.
   * Ein Aufruf von „livePipelineDelete“ zum Löschen der Livepipeline.
   * Ein Aufruf von „pipelineTopologyDelete“ zum Löschen der Topologie.
   * Ein abschließender Aufruf von „pipelineTopologyList“, um zu zeigen, dass die Liste jetzt leer ist.

## <a name="interpret-the-results"></a>Interpretieren der Ergebnisse 

Wenn Sie die Livepipeline ausführen, werden vom Video Analyzer-Modul bestimmte Diagnose- und Betriebsereignisse an die IoT Edge-Hub-Instanz gesendet. Diese Ereignisse sind die Meldungen, die im **Ausgabefenster** von Visual Studio Code angezeigt werden. Sie enthalten einen Abschnitt „body“ und einen Abschnitt „applicationProperties“. Informationen zu diesen Abschnitten finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden die Anwendungseigenschaften und der Inhalt von „body“ durch das Video Analyzer-Modul definiert.

## <a name="diagnostics-events"></a>Diagnoseereignisse

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis 

Wenn die Livepipeline aktiviert wird, wird vom RTSP-Quellknoten versucht, eine Verbindung mit dem RTSP-Server herzustellen, der im RTSP-Simulatorcontainer ausgeführt wird. Bei erfolgreich hergestellter Verbindung wird dieses Ereignis ausgegeben:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```


* Die Meldung ist ein Diagnoseereignis (MediaSessionEstablished). Hierdurch wird angegeben, dass vom RTSP-Quellknoten (Antragsteller) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Knoten in der Pipelinetopologie, über den die Nachricht generiert wurde. In diesem Fall stammt die Nachricht vom RTSP-Quellknoten.
* Der Abschnitt „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
* Der Abschnitt „eventTime“ gibt den Zeitpunkt des Ereignisses an.
* Der Abschnitt „body“ enthält Daten zum Diagnoseereignis, in diesem Fall die [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Details.

## <a name="operational-events"></a>Betriebsereignisse

Nachdem die Pipeline eine Zeit lang ausgeführt wird, trifft früher oder später ein Ereignis vom objectCounter-Modul ein. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [avasample-iot-edge-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime&quot;: &quot;2020-05-17T17:53:44.062Z"
  }
}
```

Der Abschnitt „applicationProperties“ enthält die Ereigniszeit. Dies ist der Zeitpunkt, zu dem das objectCounter-Modul beobachtet hat, dass die Ergebnisse des yolov3-Moduls interessierende Objekte (LKW) enthalten.

Sie werden möglicherweise das Auftreten weiterer dieser Ereignisse feststellen, wenn weitere LKW im Video erkannt werden.

### <a name="recordingstarted-event"></a>RecordingStarted-Ereignis

Fast unmittelbar nach dem Senden des Ereignisses durch den Objektzähler wird ein Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted** angezeigt:

```
[IoTHubMonitor] [5:53:46 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStarted",
    "eventTime": "2021-04-09T09:42:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name der Video Analyzer-Videoressource, auf der Videodaten aufgezeichnet werden.

### <a name="recordingavailable-event"></a>RecordingAvailable-Ereignis

Wie der Name bereits vermuten lässt, wird das Ereignis „RecordingStarted“ gesendet, wenn die Aufzeichnung begonnen hat. Unter Umständen wurden aber noch keine Mediendaten auf die Videoressource hochgeladen. Nachdem vom Videosenke-Knoten Mediendaten hochgeladen wurden, wird ein Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable** ausgegeben:

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingAvailable",
    "eventTime": "2021-04-09T09:43:38.1280000Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass genügend Daten auf die Videoressource geschrieben wurden, um die Wiedergabe des Videos über Player oder Clients zu starten. Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name der Video Analyzer-Ressource, auf der Videodaten aufgezeichnet werden.

### <a name="recordingstopped-event"></a>Ereignis „RecordingStopped“

Wenn Sie die Livepipeline deaktivieren, beendet der Videosenke-Knoten die Aufzeichnung von Medien. Das folgende Ereignis vom Typ **Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped** wird ausgegeben:

```
[IoTHubMonitor] [11:33:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "videoName",
    "outputLocation&quot;: &quot;sample-evr-video"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzers.Pipeline.Operational.RecordingStopped",
    "eventTime": "2021-04-10T11:33:31.051Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Durch dieses Ereignis wird angegeben, dass die Aufzeichnung beendet wurde. Der Abschnitt „subject“ in „applicationProperties“ verweist auf den Videosenke-Knoten in der Livepipeline, von dem diese Nachricht generiert wurde. Der Textabschnitt enthält Informationen über den Ausgabespeicherort. In diesem Fall ist dies der Name der Video Analyzer-Ressource, auf der Videodaten aufgezeichnet werden.

## <a name="playing-back-the-recording"></a>Wiedergeben der Aufzeichnung

Sie können die von der Livepipeline erstellte Video Analyzer-Videoressource untersuchen, indem Sie sich beim Azure-Portal anmelden und das Video anzeigen.
1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie unter den Ressourcen Ihres Abonnements nach Ihrem Video Analyzer-Konto, und öffnen Sie den Kontobereich.
1. Wählen Sie in der Liste **Video Analyzer** die Option **Videos** aus.
1. Ein Video mit dem Namen `sample-evr-video` ist aufgeführt. Dies ist der Name, den Sie in Ihrer Datei mit der Pipelinetopologie ausgewählt haben.
1. Wählen Sie das Video aus.
1. Die Seite mit den Videodetails wird geöffnet, und die Wiedergabe sollte automatisch gestartet werden.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [clean-up-resources](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras mit RTSP-Unterstützung finden Sie auf der [Seite für ONVIF-konforme Produkte](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die den Profilen G, S oder T entsprechen.
* Verwenden Sie ein AMD64- oder x64-Linux-Gerät (anstelle eines virtuellen Azure-Linux-Computers). Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md). Befolgen Sie anschließend die Anweisungen in der Schnellstartanleitung [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md), um das Gerät für Azure IoT Hub zu registrieren.
