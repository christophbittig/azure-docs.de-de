---
title: Analysieren von Livevideos mithilfe der Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ über gRPC mit Azure Video Analyzer
description: In diesem Tutorial erfahren Sie, wie Sie die Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ verwenden, um Livevideofeeds einer (simulierten) IP-Kamera zu analysieren.
ms.topic: tutorial
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a3436dced8129aa984d30a0f2b4d6bcfecb5913
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484211"
---
# <a name="tutorial-analyze-live-video-with-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutorial: Analysieren von Livevideos mit der Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ 

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial erfahren Sie, wie Sie die Edge AI Extension „Intel OpenVINO™ DL Streamer“ von Intel verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Außerdem wird veranschaulicht, wie dieser Rückschlussserver Ihnen Zugriff auf diverse Modelle zum Erkennen von Objekten (z. B. eine Person, ein Fahrzeug oder ein Fahrrad) oder für die Objektklassifizierung (z. B. Fahrzeugzuordnungen) sowie ein Modell für die Objektnachverfolgung (Personen, Fahrzeuge und Fahrräder). Die Integration mit dem gRPC-Modul ermöglicht Ihnen das Senden von Videoframes an den KI-Rückschlussserver. Die Ergebnisse werden dann an den IoT Edge-Hub gesendet. Wenn Sie diesen Rückschlussdienst auf dem gleichen Computeknoten wie Azure Video Analyzer ausführen, können Sie Videodaten über gemeinsam genutzten Speicher senden. Dies ermöglicht das Ausführen von Rückschlüssen in der Geschwindigkeit der Bildfrequenz des Livevideofeeds (30 Frames/Sek.). 

In diesem Tutorial werden ein virtueller Azure-Computer als simuliertes IoT Edge-Gerät und ein simulierter Livevideostream verwendet. Es basiert auf Beispielcode, der in C# geschrieben ist, und baut auf der Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) auf.

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen wird ein kurzes Parkplatzvideo auf den virtuellen Linux-Computer in Azure kopiert, den Sie als IoT Edge-Gerät verwenden. In diesem Tutorial wird die Videodatei verwendet, um einen Livestream zu simulieren.

Öffnen Sie eine Anwendung wie etwa den [VLC Media Player](https://www.videolan.org/vlc/). Drücken Sie STRG+N, und fügen Sie dann einen Link zum [Video](https://avamedia.blob.core.windows.net/public/lots_015.mkv) ein, um die Wiedergabe zu starten. Das Video zeigt Fahrzeuge auf einem Parkplatz. Die meisten davon sind geparkt. Ein einzelnes Fahrzeug bewegt sich.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

In diesem Tutorial werden Video Analyzer und die Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ von Intel verwendet, um Objekte wie Fahrzeuge, Personen oder Fahrräder zu erkennen und Fahrzeuge zu klassifizieren oder zu verfolgen. Die resultierenden Rückschlussereignisse werden im IoT Edge-Hub veröffentlicht.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.png" alt-text="Übersicht über die Azure Video Analyzer-Pipeline mit dem Edge-KI-Modul „DL Streamer“ von Intel":::

In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Ein [RTSP-Quellknoten](../pipeline.md#rtsp-source) bezieht den Videofeed von diesem Server und sendet Videoframes an den Knoten des [gRPC-Erweiterungsprozessors](../pipeline.md#grpc-extension-processor). 

Dieser gRPC-Erweiterungsprozessor verwendet decodierte Videoeinzelbilder als Eingabe und leitet diese an einen [gRPC](../terminology.md#grpc)-Endpunkt weiter, der von einem gRPC-Server zur Verfügung gestellt wird. Der Knoten unterstützt das Übertragen von Daten mithilfe von [Shared Memory](https://en.wikipedia.org/wiki/Shared_memory) oder durch direktes Einbetten der Inhalte in den Text von gRPC-Nachrichten. Darüber hinaus weist der Knoten einen integrierten Bildformatierer auf, um Videoeinzelbilder zu skalieren und zu codieren, bevor sie an den gRPC-Endpunkt weitergeleitet werden. Der Skalierer verfügt über Optionen für das Beibehalten, Auffüllen oder Strecken des Seitenverhältnisses. Der Bildcodierer unterstützt die Formate JPEG, PNG und BMP. Weitere Informationen zum Prozessor finden Sie [hier](../pipeline.md#grpc-extension-processor).

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Informationen zum Edge AI Extension-Modul „OpenVINO™ DL Streamer“


Das Edge AI Extension-Modul „OpenVINO™ DL Streamer“ ist ein Microservice, der auf Video Analytics Serving (VA Serving) von Intel basiert. Dabei handelt es sich um eine Anwendung, die Videoanalysepipelines mit Daten versorgt, die mit OpenVINO™ DL Streamer erstellt wurden. Entwickler können entschlüsselte Videoframes an das Edge AI Extension-Modul senden, das die Erkennung, Klassifizierung oder Nachverfolgung durchführt und dann die Ergebnisse zurückgibt. Durch das KI-Erweiterungsmodul werden gRPC-APIs verfügbar gemacht, die mit Videoanalyseplattformen wie Azure Video Analyzer von Microsoft kompatibel sind.

Zur Erstellung komplexer Hochleistungslösungen für die Videoanalyse empfiehlt es sich, das Azure Video Analyzer-Modul mit einer leistungsfähigen Rückschluss-Engine zu kombinieren, die das Skalierungspotential im Edgebereich nutzen kann. In diesem Tutorial werden Rückschlussanforderungen an die Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ gesendet. Dabei handelt es sich um ein Edge-Modul, das für die Zusammenarbeit mit Azure Video Analyzer konzipiert ist.

Das erste Release dieses Rückschlussservers bietet Zugriff auf folgende [Modelle](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options):

- „object_detection“ für „person_vehicle_bike_detection“ ![Objekterkennung für Fahrzeuge](./media/use-intel-openvino-tutorial/object-detection.png)

- „object_classification“ für „vehicle_attributes_recognition“ ![Objektklassifizierung für Fahrzeuge](./media/use-intel-openvino-tutorial/object-classification.png)

- „object_tracking“ für „person_vehicle_bike_tracking“ ![Objektnachverfolgung für Personenfahrzeuge](./media/use-intel-openvino-tutorial/object-tracking.png)

Für den schnellen Start werden vorab geladene Objekterkennungs-, Objektklassifizierungs- und Objektnachverfolgungspipelines verwendet. Darüber hinaus sind die vorab geladenen Modelle [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) und [vehicle-attributes-recognition-barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md) enthalten.

> [!NOTE]
> Wenn Sie das Edge AI Extension-Modul „OpenVINO™ DL Streamer“ von Intel und die enthaltene Software herunterladen und verwenden, stimmen Sie den gemäß der [Lizenzvereinbarung](https://www.intel.com/content/www/us/en/legal/terms-of-use.html) geltenden Geschäftsbedingungen zu.
> Intel setzt sich für die Achtung der Menschenrechte ein und ist darauf bedacht, eine Mitschuld an Menschenrechtsverletzungen zu vermeiden. Weitere Informationen finden Sie in den [globalen Menschenrechtsprinzipien von Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkte und Software von Intel dürfen nur in Anwendungen verwendet werden, die kein international anerkanntes Menschenrecht verletzen oder zu einer solchen Verletzung beitragen.

Sie können die Flexibilität der verschiedenen Pipelines für Ihren speziellen Anwendungsfall verwenden, indem Sie einfach die Umgebungsvariablen für die Pipeline in Ihrer Bereitstellungsvorlage anpassen. Dies ermöglicht es Ihnen, schnell das Pipelinemodell zu wechseln. In Kombination mit Azure Video Analyzer dauert das Ändern der Medienpipeline und des Rückschlussmodells nur wenige Sekunden.

In diesem Lernprogramm lernen Sie Folgendes:

1. Einrichten Ihrer Entwicklungsumgebung
1. Bereitstellen der erforderlichen Edgemodule
1. Erstellen und Bereitstellen der Livepipeline
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="deploy-the-required-modules"></a>Bereitstellen der erforderlichen Module

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf die Datei *src/edge/deployment.openvino.grpc.template.json*, und wählen Sie **IoT Edge-Bereitstellungsmanifest generieren** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/generate-deployment-manifest.png" alt-text="Generieren eines IoT Edge-Bereitstellungsmanifests":::
1. Die Manifestdatei *deployment.openvino.grpc.amd64.json* wird im Ordner *src/edge/config* erstellt.
1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/deployment.openvino.grpc.amd64.json*, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/analyze-live-video-use-your-model-http/deployment-single-device.png" alt-text= "Create Deployment for a Single Device":::
1. Wählen Sie **avasample-iot-edge-device** aus, wenn Sie zum Auswählen eines IoT Hub-Geräts aufgefordert werden.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

    * Das Video Analyzer-Edgemodul mit dem Namen **avaedge**
    * Das Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert 
    * Das Modul **avaextension**. Hierbei handelt es sich um das Modul „Intel OpenVINO DL Streamer“ mit Objekterkennungs- Klassifizierungs- und Verfolgungsmodell, das maschinelles Sehen auf Bilder anwendet und mehrere Klassen von Objekttypen zurückgibt.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/vscode-common-screenshots/avaextension.png" alt-text= "OpenVINO object detection model":::

> [!NOTE]
> Außerdem ist die Vorlage *deployment.openvino.grpc.cpu.template.json* enthalten, die die CPU-, VPU- und GPU-Unterstützung für das Edge-KI-Erweiterungsmodul „Intel OpenVINO™ DL Streamer“ ermöglicht (sofern verfügbar). Diese Vorlagen verweisen auf das Docker-Hub-Image von Intel.

### <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

Klicken Sie mit der rechten Maustaste auf das Azure Video Analyzer-Gerät, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus. Sie müssen diesen Schritt ausführen, um die IoT Hub-Ereignisse im **Ausgabefenster** von Visual Studio Code zu überwachen.

![Starten der Überwachung](./media/quickstarts/start-monitoring-iot-hub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Ausführen des Beispielprogramms zum Erkennen von Fahrzeugen/Personen/Fahrrädern
Wenn Sie die [Pipelinetopologie](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `grpcExtensionAddress` auf `tcp://avaExtension:5001` festgelegt wurde. Im Vergleich zum Tutorial *httpExtensionOpenVINO* sind hier keine Änderung an der URL für den gRPC-Server erforderlich. Stattdessen wird das Modul mithilfe von `extensionConfiguration` in der Vorgangsdatei angewiesen, eine bestimmte Pipeline auszuführen. Ohne Angabe wird für „person_vehicle_bike_detection“ standardmäßig „object_detection“ verwendet. Sie können jedoch auch mit anderen unterstützten Pipelines experimentieren.

1. Bearbeiten Sie die Datei *operations.json*:
    * Ändern Sie den Link zur Livepipelinetopologie:

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"`

    * Bearbeiten Sie unter `pipelineTopologySet` den Namen der Livepipelinetopologie, sodass er mit dem Wert im vorherigen Link übereinstimmt:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Ändern Sie unter `pipelineTopologyDelete` den Namen:

      `"name": "InferencingWithOpenVINOgRPC"`
    
    * Ändern Sie unter `rtspUrl` die URL in `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`.

Fügen Sie nach den RTSP-Parametern die folgende Erweiterungskonfiguration (extensionConfiguration) hinzu:

```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
}
```


Ihr App-Modul (`operations.json`) sollte wie folgt aussehen:

```
{
  "apiVersion": "1.1,
  "operations": [
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue."
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the activating steps."
          }
      },
      {
          "opName": "pipelineTopologySet",
          "opParams": {
              "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json"
          }
      },
      {
          "opName": "livePipelineSet",
          "opParams": {
              "name": "Sample-Pipeline-1",
              "properties": {
                  "topologyName": "InferencingWithOpenVINOgRPC",
                  "description": "Sample pipeline description",
                  "parameters": [
                      {
                          "name": "rtspUrl",
                          "value": "rtsp://rtspsim:554/media/lots_015.mkv"
                      },
                      {
                          "name": "rtspUserName",
                          "value": "testuser"
                      },
                      {
                          "name": "rtspPassword",
                          "value": "testpassword"
                      },
                      {
                        "name": "extensionConfiguration",
                        "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
                      }
                  ]
              }
          }
      },
      {
          "opName": "livePipelineActivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "WaitForInput",
          "opParams": {
              "message": "Press Enter to continue. This is start the deactivating steps."
          }
      },
      {
          "opName": "livePipelineDeactivate",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineDelete",
          "opParams": {
              "name": "Sample-Pipeline-1"
          }
      },
      {
          "opName": "livePipelineList",
          "opParams": {}
      },
      {
          "opName": "pipelineTopologyDelete",
          "opParams": {
              "name": "InferencingWithOpenVINOgRPC"
          }
      },
      {
          "opName": "pipelineTopologyList",
          "opParams": {}
      }
  ]
}
```

Die Verwendung von `extensionConfiguration` ermöglicht es, eine Reihe von Daten an das Edge-KI-Modul „DL Streamer“ von Intel zu übergeben. Mit den richtigen Daten können Sie das Edge-KI-Modul anweisen, ein bestimmtes Modell für die jeweilige Pipeline zu verwenden. So können Sie beispielsweise das oben verwendete Modell „object_detection“ durch andere unterstützte Modelle wie Klassifizierung oder Nachverfolgung ersetzen. Im Anschluss finden Sie einige entsprechende Beispiele:

Klassifizierung:
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_classification\",\"version\":\"vehicle_attributes_recognition\"}}"
}
```

Nachverfolgung:
```
{
    "name": "extensionConfiguration",
    "value": "{\"pipeline\":{\"name\":\"object_tracking\",\"version\":\"person_vehicle_bike_tracking\"}}"
}
```

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von *operations.json* beginnt mit Aufrufen der direkten Methoden `pipelineTopologyList` und `livePipelineList`. Wenn Sie nach dem Durchlaufen vorheriger Schnellstartanleitungen/Tutorials eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und die Ausführung wird angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.

    Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

     * Ein Aufruf von `pipelineTopologySet`, der die vorherige `topologyUrl` verwendet
     * Ein Aufruf von `livePipelineSet`, der den folgenden Textkörper verwendet:

         ```
         {
           "@apiVersion": "1.1",
           "name": "Sample-Pipeline-1",
           "properties": {
             "topologyName": "InferencingWithGrpcExtension",
             "description": "Sample pipeline description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               },
               {
                 "name": "grpcExtensionAddress",
                 "value": "tcp://avaextension:5001"
               },
               {
                 "name": "extensionConfiguration",
                 "value": "{\"pipeline\":{\"name\":\"object_detection\",\"version\":\"person_vehicle_bike_detection\"}}"
               }
             ]
           }
         }
         ```

     * Ein Aufruf von `livePipelineActivate`, mit dem die Pipeline und der Videodatenfluss gestartet werden
1. Die Ausgabe im **Terminalfenster** wird mit der Aufforderung `Press Enter to continue` angehalten. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Nachrichten angezeigt, die vom Video Analyzer-Modul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieses Tutorials sind diese Meldungen beschrieben.
1. Die Livepipeline wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Livepipeline zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE. 

    Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
      * Mit dem Aufruf von `livePipelineDeactivate` wird die Pipeline deaktiviert.
      * Mit dem Aufruf von `livePipelineDelete` wird die Pipeline gelöscht.
      * Mit dem Aufruf von `pipelineTopologyDelete` wird die Topologie gelöscht.
      * Ein abschließender Aufruf von `pipelineTopologyList` zeigt, dass die Liste leer ist.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie die Livepipeline ausführen, durchlaufen die Ergebnisse vom Knoten des gRPC-Erweiterungsprozessors den Knoten der IoT Hub-Nachrichtensenke und werden an den IoT-Hub gesendet. Die im Ausgabefenster angezeigten Nachrichten enthalten jeweils den Abschnitt `body`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten wird vom Video Analyzer-Modul der Inhalt des Texts definiert. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Livepipeline aktiviert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
}
```

Für die obige Ausgabe gilt Folgendes: 

* Bei der Meldung handelt es sich um ein Diagnoseereignis (`MediaSessionEstablished`). Hierdurch wird angegeben, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Der Abschnitt `sdp` enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der gRPC-Erweiterungsprozessorknoten empfängt Rückschlussergebnisse von der Edge AI Extension „OpenVINO™ DL Streamer“ von Intel. Anschließend gibt er die Ergebnisse über den Knoten der IoT Hub-Nachrichtensenke als Rückschlussereignisse aus. 

In diesen Ereignissen ist der Typ auf `entity` festgelegt, um anzuzeigen, dass es sich um eine Entität wie etwa ein Auto oder einen LKW handelt. 

Im folgenden Beispiel wurde ein Fahrzeug mit Konfidenzwerten oberhalb von 0,9 erkannt.

```
[IoTHubMonitor] [3:10:23 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "timestamp": 145819820073974,
  "inferences": [
    {
      "type": "entity",
      "subtype": "vehicleDetection",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9147264
        },
        "box": {
          "l": 0.6853116,
          "t": 0.5035262,
          "w": 0.04322505,
          "h": 0.03426218
        }
      }
    }
```

Beachten Sie in den Meldungen die folgenden Details:

* Der Abschnitt `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `inferences`.
* Im Abschnitt `inferences` ist angegeben, dass `type` auf `entity` festgelegt ist. Dieser Abschnitt enthält zusätzliche Daten zur Entität.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein x64-Linux-Gerät anstelle einer Azure-Linux-VM. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md) befolgen. Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md) ausführen.
