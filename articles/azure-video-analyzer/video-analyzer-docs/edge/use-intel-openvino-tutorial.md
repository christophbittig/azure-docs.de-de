---
title: Analysieren von Livevideos mit der KI-Erweiterung für OpenVINO™ Model Server von Intel
description: In diesem Tutorial wird ein von Intel bereitgestellter KI-Modellserver mit vorab trainierten Modellen verwendet, um den Livevideofeed einer (simulierten) IP-Kamera zu analysieren.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
titleSuffix: Azure
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2e6e14849bfa00862b2fd87a0e42d88b315ab14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490703"
---
# <a name="tutorial-analyze-live-video-using-openvino-model-server--ai-extension-from-intel"></a>Tutorial: Analysieren von Livevideos mit der KI-Erweiterung für OpenVINO™ Model Server von Intel 

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial wird veranschaulicht, wie Sie die [KI-Erweiterung für OpenVINO™ Model Server von Intel](https://aka.ms/ava-intel-ovms) verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Es wird gezeigt, wie Sie über diesen Rückschlussserver auf Modelle für die Objekterkennung (Person, Fahrzeug oder Fahrrad) sowie auf ein Modell für die Klassifizierung von Fahrzeugen zugreifen. Eine Teilmenge der Frames aus dem Livevideofeed wird an diesen Rückschlussserver gesendet, und die Ergebnisse werden an den IoT Edge-Hub übermittelt.

In diesem Tutorial werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet. Die Grundlage hierfür ist in C# geschriebener Beispielcode.

[!INCLUDE [use-x86-64](./includes/common-includes/use-x86-64.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Informationen zur KI-Erweiterung für OpenVINO™ Model Server von Intel

Die Intel®-Distribution des [OpenVINO™-Toolkits](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual Inference and Neural network Optimization) ist ein kostenloses Softwarekit, das Entwickler und Data Scientists dabei unterstützt, Workloads für maschinelles Sehen zu beschleunigen, Deep Learning-Rückschlüsse und -Bereitstellungen zu optimieren und eine mühelose, heterogene Ausführung auf Intel®-Plattformen zwischen Edge und Cloud zu ermöglichen. Es umfasst das Intel® Deep Learning Deployment Toolkit mit Modelloptimierung und Rückschluss-Engine sowie das Repository [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo) mit über 40 optimierten vortrainierten Modellen.

Zur Erstellung komplexer Hochleistungslösungen für die Videoanalyse empfiehlt es sich, das Video Analyzer-Modul mit einer leistungsfähigen Rückschluss-Engine zu kombinieren, mit der das Skalierungspotenzial im Edgebereich ausgenutzt werden kann. In diesem Tutorial werden Rückschlussanforderungen an die [KI-Erweiterung für OpenVINO™ Model Server von Intel](https://aka.ms/ava-intel-ovms) gesendet. Hierbei handelt es sich um ein Edgemodul, das für die Zusammenarbeit mit Video Analyzer konzipiert wurde. Dieses Rückschlussservermodul enthält OpenVINO™ Model Server (OVMS) – einen auf dem OpenVINO™-Toolkit basierenden Rückschlussserver, der speziell für Workloads für maschinelles Sehen optimiert und für Intel®-Architekturen entwickelt wurde. OVMS wurde mit einer Erweiterung für den unkomplizierten Austausch von Videoframes und Rückschlussergebnissen zwischen dem Rückschlussserver und dem Video Analyzer-Modul versehen. Hiermit können Sie alle vom OpenVINO™-Toolkit unterstützten Modelle ausführen. (Zum Anpassen des Rückschlussservermoduls ändern Sie den [Code](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper).) Darüber hinaus steht eine umfassende Palette an Beschleunigungsmechanismen zur Auswahl, die über Intel®-Hardware bereitgestellt werden. Hierzu zählen CPUs (Atom, Core, Xeon), FPGAs und VPUs.

Das erste Release dieses Rückschlussservers bietet Zugriff auf folgende [Modelle](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models):

- Fahrzeugerkennung (Rückschluss-URL: http://{Modulname}:4000/vehicleDetection)
- Personen-/Fahrzeug-/Fahrraderkennung (Rückschluss-URL: http://{Modulname}:4000/personVehicleBikeDetection)
- Fahrzeugklassifizierung (Rückschluss-URL: http://{Modulname}:4000/vehicleClassification)
- Gesichtserkennung (Rückschluss-URL: http://{Modulname}:4000/faceDetection)

> [!NOTE]
> Wenn Sie das Edge-Modul der KI-Erweiterung für OpenVINO™ Model Server von Intel und die darin enthaltene Software herunterladen und verwenden, stimmen Sie den Geschäftsbedingungen der [Lizenzvereinbarung](https://www.intel.com/content/www/us/en/legal/terms-of-use.html) zu.
> Intel setzt sich für die Achtung der Menschenrechte ein und ist darauf bedacht, eine Mitschuld an Menschenrechtsverletzungen zu vermeiden. Weitere Informationen finden Sie in den [globalen Menschenrechtsprinzipien von Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkte und Software von Intel dürfen nur in Anwendungen verwendet werden, die kein international anerkanntes Menschenrecht verletzen oder zu einer solchen Verletzung beitragen.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/http-extension-with-vino.png" alt-text="Übersicht":::

In diesem Diagramm ist der Fluss der Signale in diesem Schnellstart dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](../pipeline.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](../pipeline-extension.md#http-extension-processor). 

Der Knoten des HTTP-Erweiterungsprozessors übernimmt hierbei die Rolle eines Proxys. Er wählt einen Teil der eingehenden Videoframes aus und konvertiert sie in Bilder. Anschließend leitet er das Bild per REST an ein anderes Edge-Modul weiter, das KI-Modelle hinter einem HTTP-Endpunkt ausführt. In diesem Beispiel wird als Edge-Modul die KI-Erweiterung für OpenVINO™ Model Server von Intel verwendet. Der Knoten des HTTP-Erweiterungsprozessors erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink). Letzterer Knoten sendet diese Ereignisse dann an den [IoT Edge-Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).

In diesem Lernprogramm lernen Sie Folgendes:

1. Einrichten Ihrer Entwicklungsumgebung
1. Bereitstellen der erforderlichen Edgemodule
1. Erstellen und Bereitstellen der Livepipeline
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

### <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen wird ein kurzes [Parkplatzvideo](https://avamedia.blob.core.windows.net/public/lots_015.mkv) auf den virtuellen Linux-Computer in Azure kopiert, den Sie als IoT Edge-Gerät verwenden. In dieser Schnellstartanleitung wird die Videodatei verwendet, um einen Livestream zu simulieren.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LUbN]

## <a name="deploy-the-required-modules"></a>Bereitstellen der erforderlichen Module

Als Teil der Voraussetzungen haben Sie den Beispielcode in einen Ordner heruntergeladen. Führen Sie diese Schritte aus, um die erforderlichen Module bereitzustellen.

1. Klicken Sie in Visual Studio Code mit der rechten Maustaste auf die Datei *src/edge/deployment.openvino.template.json*, und wählen Sie **IoT Edge-Bereitstellungsmanifest generieren** aus.   
1. Die Manifestdatei *deployment.openvino.amd64.json* wird im Ordner *src/edge/config* erstellt.
1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/deployment.openvino.amd64.json*, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.
1. Wählen Sie **avasample-iot-edge-device** aus, wenn Sie zum Auswählen eines IoT Hub-Geräts aufgefordert werden.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:
    * Das Video Analyzer-Edgemodul mit dem Namen **avaedge**
    * Das Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert 
    * Das Modul **openvino** (KI-Erweiterungsmodul für OpenVINO™ Model Server von Intel)
 
## <a name="create-and-activate-the-live-pipeline"></a>Erstellen und Aktivieren der Livepipeline

### <a name="edit-the-sample-code"></a>Bearbeiten des Beispielcodes
1. Navigieren Sie in Visual Studio Code zu *src/cloud-to-device-console-app*, und bearbeiten Sie die Datei *operations.json*:
    * Ändern Sie den Link zur Livepipeline-Topologie:

        `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json"`

    * Bearbeiten Sie unter `livePipelineSet` den Namen der Livepipelinetopologie, sodass er mit dem Wert im vorherigen Link übereinstimmt:

      `"topologyName" : "InferencingWithOpenVINO"`

    * Ändern Sie unter `pipelineTopologyDelete` den Namen:

      `"name": "InferencingWithOpenVINO"`

### <a name="run-the-sample-program-to-detect-vehicles"></a>Ausführen des Beispielprogramms für die Fahrzeugerkennung
Wenn Sie die Pipelinetopologie (`pipelineTopologyUrl`) für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `inferencingUrl` auf `http://openvino:4000/vehicleDetection` festgelegt wurde. Dies bedeutet, dass der Rückschlussserver versucht, Fahrzeuge im Livevideo zu erkennen.

1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von *operations.json* beginnt mit Aufrufen der direkten Methoden `pipelineTopologyList` und `livePipelineList`. Wenn Sie nach dem Durcharbeiten der vorherigen Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben. Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

     * Ein Aufruf von `pipelineTopologySet`, der die vorherige `pipelineTopologyUrl` verwendet
     * Ein Aufruf von `livePipelineSet`, der den folgenden Textkörper verwendet:

         ```json
            {
              "@apiVersion": "1.1",
              "name": "Sample-Pipeline-1",
              "properties": {
                "topologyName": "InferencingWithOpenVINO",
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

     * Ein Aufruf von `livePipelineActivate`, mit dem die Pipeline und der Videodatenfluss gestartet werden
1. Die Ausgabe im **Terminalfenster** wird mit der Aufforderung `Press Enter to continue` angehalten. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Nachrichten angezeigt, die vom Video Analyzer-Modul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
1. Die Livepipeline wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Livepipeline zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE. 

    Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
      * Mit dem Aufruf von `livePipelineDeactivate` wird die Pipeline deaktiviert.
      * Mit dem Aufruf von `livePipelineDelete` wird die Pipeline gelöscht.
      * Mit dem Aufruf von `pipelineTopologyDelete` wird die Topologie gelöscht.
      * Ein abschließender Aufruf von `pipelineTopologyList` zeigt, dass die Liste leer ist.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie die Livepipeline ausführen, durchlaufen die Ergebnisse vom Knoten des HTTP-Erweiterungsprozessors den Knoten der IoT Hub-Nachrichtensenke und werden an den IoT-Hub gesendet. Die im **Ausgabefenster** angezeigten Nachrichten enthalten jeweils den Abschnitt `body`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden vom Video Analyzer-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Livepipeline aktiviert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird im **Ausgabefenster** ein Ereignis der folgenden Art angezeigt.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoanalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzers.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-04-09T09:42:18.1280000Z"
  }
}
```

Für die obige Ausgabe gilt Folgendes: 

* Die Nachricht ist ein Diagnoseereignis (**MediaSessionEstablished**). Hierdurch wird angegeben, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* Der Abschnitt `sdp` enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der Knoten des HTTP-Erweiterungsprozessors empfängt Rückschlussergebnisse vom KI-Erweiterungsmodul für OpenVINO™ Model Server. Anschließend gibt er die Ergebnisse über den Knoten der IoT Hub-Nachrichtensenke als Rückschlussereignisse aus. 

In diesen Ereignissen ist der Typ auf `entity` festgelegt, um anzuzeigen, dass es sich um eine Entität wie etwa ein Auto oder einen LKW handelt. Im folgenden Beispiel für das `body`-Element eines Ereignisses dieser Art wurde ein Fahrzeug mit Konfidenzwerten oberhalb von 0,9 erkannt.

```json
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

## <a name="run-the-sample-program-to-detect-persons-vehicles-or-bikes"></a>Ausführen des Beispielprogramms für die Erkennung von Personen, Fahrzeugen oder Fahrrädern
Wenn Sie ein anderes Modell verwenden möchten, müssen Sie sowohl die Pipelinetopologie als auch die Datei `operations.json` ändern.

Kopieren Sie die Pipelinetopologie (in `pipelineTopologyUrl` verwendete URL) in eine lokale Datei, z. B. `C:\TEMP\topology.json`. Öffnen Sie diese Kopie, und ändern Sie den Wert `inferencingUrl` in `http://openvino:4000/personVehicleBikeDetection`.

Navigieren Sie als Nächstes in Visual Studio Code zum Ordner *src/cloud-to-device-console-app*, und öffnen Sie die Datei `operations.json`. Bearbeiten Sie die Zeile mit `pipelineTopologyUrl` wie folgt:

```
      "pipelineTopologyFile" : "C:\\TEMP\\topology.json" 
```
Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Die Rückschlussergebnisse ähneln hinsichtlich des Schemas den Ergebnissen des Fahrzeugerkennungsmodells. Lediglich `subtype` ist auf `personVehicleBikeDetection` festgelegt.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Ausführen des Beispielprogramms für die Fahrzeugklassifizierung
Öffnen Sie in Visual Studio Code die lokale Kopie von `topology.json` aus dem vorherigen Schritt, und ändern Sie den Wert von `inferencingUrl` in `http://openvino:4000/vehicleClassification`. Wenn Sie das vorherige Beispiel für die Erkennung von Personen, Fahrzeugen oder Fahrrädern ausgeführt haben, muss die Datei `operations.json` nicht erneut geändert werden.

Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Hier sehen Sie ein Beispiel für ein Klassifizierungsergebnis:

```json
{
  "timestamp": 145819896480527,
  "inferences": [
    {
      "type": "classification",
      "subtype": "color",
      "classification": {
        "tag": {
          "value": "gray",
          "confidence": 0.683415
        }
      }
    },
    {
      "type": "classification",
      "subtype": "type",
      "classification": {
        "tag": {
          "value": "truck",
          "confidence": 0.9978394
        }
      }
    }
  ]
}

```

## <a name="run-the-sample-program-to-detect-faces"></a>Ausführen des Beispielprogramms für die Gesichtserkennung
Öffnen Sie in Visual Studio Code die lokale Kopie von `topology.json` aus dem vorherigen Schritt, und ändern Sie den Wert von `inferencingUrl` in `http://openvino:4000/faceDetection`. Wenn Sie das vorherige Beispiel für die Erkennung von Personen, Fahrzeugen oder Fahrrädern ausgeführt haben, muss die Datei `operations.json` nicht erneut geändert werden.

Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Topologie auszuführen. Im Anschluss sehen Sie ein Beispiel für ein Erkennungsergebnis. (Hinweis: Das oben verwendete Parkplatzvideo enthält keine erkennbaren Gesichter. Verwenden Sie daher ein anderes Video, um dieses Modell zu testen.)

```json
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  }
```
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein lokales x64-Linux-Gerät anstelle einer Azure-Linux-VM. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md) befolgen. Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md) ausführen.
