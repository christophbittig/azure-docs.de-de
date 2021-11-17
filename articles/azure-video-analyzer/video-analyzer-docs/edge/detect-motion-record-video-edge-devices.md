---
title: 'Erkennen von Bewegung und Aufzeichnen von Videos auf Edgegeräten: Azure'
description: Verwenden Sie Azure Video Analyzer, um den Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Es wird veranschaulicht, wie Bewegung im Video erkannt wird, damit dann ein MP4-Videoclip im lokalen Dateisystem auf dem Edgegerät aufgezeichnet werden kann. Für die Schnellstartanleitung werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.
ms.topic: quickstart
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: 84dbe38e69b9dea259c570ef229927b75aefbe23
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493418"
---
# <a name="quickstart-detect-motion-and-record-video-on-edge-devices"></a>Schnellstart: Erkennen von Bewegung und Aufzeichnen von Videos auf Edgegeräten

[!INCLUDE [header](includes/edge-env.md)]

In dieser Schnellstartanleitung wird beschrieben, wie Sie mit Azure Video Analyzer den Livevideofeed einer (simulierten) IP-Kamera analysieren. Es wird veranschaulicht, wie Bewegung im Video erkannt wird, damit dann ein MP4-Videoclip im lokalen Dateisystem auf dem Edgegerät aufgezeichnet werden kann. Für die Schnellstartanleitung werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen für diese Schnellstartanleitung wird ein kurzes Video eines Parkplatzes auf die Linux-VM in Azure kopiert, die als IoT Edge-Gerät verwendet wird. Diese Videodatei wird zum Simulieren eines Livestreams für dieses Tutorial verwendet.

Öffnen Sie eine Anwendung wie beispielsweise den [VLC Media Player](https://www.videolan.org/vlc/), drücken Sie STRG+N, und fügen Sie [diesen Link](https://avamedia.blob.core.windows.net/public/lots_015.mkv) zum Video des Parkplatzes ein, um die Wiedergabe zu starten. Etwa bei der 5-Sekunden-Marke fährt ein weißes Fahrzeug über den Parkplatz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Führen Sie die folgenden Schritte aus, um per Video Analyzer die Bewegung eines Fahrzeugs zu erkennen und einen Videoclip aufzuzeichnen, der ungefähr bei der 5-Sekunden-Marke beginnt.

## <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generieren und Bereitstellen des Bereitstellungsmanifests

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/csharp/generate-deploy-deployment-manifest.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/detect-motion-record-video-edge-devices/python/generate-deploy-deployment-manifest.md)]
::: zone-end

## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

1. Öffnen Sie in Visual Studio Code die Registerkarte Erweiterungen (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie Erweiterungseinstellungen aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text= "Extension settings":::

1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text= "Show Verbose Message":::

1.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end
1. Mit dem Code in _operations.json_ werden die direkten Methoden `pipelineTopologyList` und `livePipelineList` aufgerufen. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die EINGABETASTE.

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

   Im Terminalfenster werden die nächsten Aufrufe direkter Methoden angezeigt:

   - Ein Aufruf von `pipelineTopologySet`, bei dem das „pipelineTopologyUrl“-Element verwendet wird
   - Ein Aufruf von `livePipelineSet`, der den folgenden Textkörper verwendet:

     ```
     {
       "@apiVersion": "1.1",
       "name": "Sample-Pipeline-1",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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

   - Ein Aufruf von `livePipelineActivate`, mit dem die Livepipeline und der Videodatenfluss gestartet werden
   - Ein zweiter Aufruf von `livePipelineList`, um anzuzeigen, dass sich die Livepipeline im ausgeführten Zustand befindet

1. Die Ausgabe im Terminalfenster wird angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum Ausgabefenster. Es werden die Nachrichten angezeigt, die vom Video Analyzer-Edgemodul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
1. Die Pipelinetopologie wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Pipelinetopologie zurück zum Terminalfenster, und drücken Sie die EINGABETASTE.

Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:

- Mit dem Aufruf von `livePipelineDeactivate` wird die Livepipeline deaktiviert.
- Mit dem Aufruf von `livePipelineDelete` wird die Livepipeline gelöscht.
- Mit dem Aufruf von `pipelineTopologyDelete` wird die Topologie gelöscht.
- Ein abschließender Aufruf von `pipelineTopologyList` zeigt, dass die Liste nun leer ist.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie die Pipelinetopologie ausführen, durchlaufen die Ergebnisse vom Knoten des Bewegungserkennungsprozessors den Knoten der IoT Hub-Senke und werden an den IoT-Hub gesendet. Die im Ausgabefenster von Visual Studio Code angezeigten Nachrichten enthalten jeweils den Abschnitt „body“ und den Abschnitt „applicationProperties“. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Video Analyzer-Edge-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Pipelinetopologie instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [ava-sample-device/avaadge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
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

- Die Nachricht ist ein Diagnoseereignis (MediaSessionEstablished). Hierdurch wird angegeben, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
- In „applicationProperties“ wird mit „subject“ auf den Knoten in der Pipelinetopologie verwiesen, über den die Nachricht generiert wurde. In diesem Fall stammt die Nachricht vom RTSP-Quellknoten.
- „eventType“ in „applicationProperties“ gibt an, dass es sich um ein Diagnoseereignis handelt.
- Der Wert „eventTime“ ist die Uhrzeit des Zeitpunkts, an dem das Ereignis eingetreten ist.
- Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum Session Description Protocol (SDP).

### <a name="recordingstarted-event"></a>RecordingStarted-Ereignis

Wenn Bewegung erkannt wird, wird der Knoten des Signalgateprozessors aktiviert, und über den Knoten der Dateisenke in der Pipelinetopologie wird mit dem Schreiben einer MP4-Datei begonnen. Der Knoten der Dateisenke sendet ein Betriebsereignis. „type“ wird auf „motion“ festgelegt, um anzugeben, dass es sich um ein Ergebnis des Bewegungserkennungsprozessors handelt. Der Wert „eventTime“ ist die UTC-Uhrzeit des Zeitpunkts, an dem die Bewegung aufgetreten ist. Weitere Informationen zu diesem Prozess finden Sie im Abschnitt [Übersicht](detect-motion-record-video-edge-devices.md#overview) dieser Schnellstartanleitung.

Hier ist ein Beispiel für diese Meldung angegeben:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation&quot;: &quot;/var/media/sampleFilesFromEVR-filesinkOutput-20210511T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-11T05:37:27.713Z",
    "dataVersion&quot;: &quot;1.0"
  }
}
```

Für die obige Meldung gilt Folgendes:

- In „applicationProperties“ wird mit „subject“ auf den Knoten in der Pipeline verwiesen, über den die Nachricht generiert wurde. In diesem Fall stammt die Meldung vom Knoten der Dateisenke.
- Mit „eventType“ in „applicationProperties“ wird angegeben, dass es sich um ein Betriebsereignis handelt.
- Der Wert „eventTime“ ist die Uhrzeit des Zeitpunkts, an dem das Ereignis eingetreten ist. Dieser Zeitpunkt liegt fünf bis sechs Sekunden nach „MediaSessionEstablished“ und nach dem Beginn des Videodatenflusses. Dieser Zeitpunkt entspricht der Marke bei 5 bis 6 Sekunden, bei der das Fahrzeug auf den Parkplatz [fährt](#review-the-sample-video).
- Der Abschnitt „body“ enthält Daten zum Betriebsereignis. In diesem Fall umfassen die Daten „outputType“ und „outputLocation“.
- Die Variable „outputType“ gibt an, dass sich diese Informationen auf den Dateipfad beziehen.
- Der Wert „outputLocation“ ist der Speicherort der MP4-Datei im Edgemodul.

### <a name="recordingstopped-and-recordingavailable-events"></a>Ereignisse „RecordingStopped“ und „RecordingAvailable“

Wenn Sie die Eigenschaften des Signalgateprozessor-Knotens in der [Pipelinetopologie](../pipeline.md) überprüfen, sehen Sie, dass die Aktivierungszeiträume auf fünf Sekunden festgelegt sind. Daher erhalten Sie ungefähr fünf Sekunden nach dem Empfang des Ereignisses **RecordingStarted** Folgendes:

- Das Ereignis **RecordingStopped**, mit dem angegeben wird, dass die Aufzeichnung beendet wurde.
- Das Ereignis **RecordingAvailable**, mit dem angegeben wird, dass die MP4-Datei nun für die Wiedergabe verwendet werden kann.

Die beiden Ereignisse werden normalerweise im Abstand von wenigen Sekunden ausgegeben.

## <a name="play-the-mp4-clip"></a>Wiedergeben des MP4-Clips

Die MP4-Dateien werden in ein Verzeichnis auf dem Edgegerät geschrieben, das Sie in der ENV-Datei mit dem Schlüssel VIDEO_OUTPUT_FOLDER_ON_DEVICE konfiguriert haben. Wenn Sie den Standardwert übernommen haben, befinden sich die Ergebnisse im Ordner /var/media/ .

Geben Sie den MP4-Clip wie folgt wieder:

1. Navigieren Sie zu Ihrer Ressourcengruppe, suchen Sie nach dem virtuellen Computer, und stellen Sie dann eine Verbindung mit dem virtuellen Computer her.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/detect-motion-record-video-edge-devices/sample-iot-edge-device.png" alt-text= "Edge device":::

1. Melden Sie sich mit den Anmeldeinformationen an, die beim Einrichten Ihrer Azure-Ressourcen generiert wurden.
1. Navigieren Sie über die Eingabeaufforderung zum entsprechenden Verzeichnis. Der Standardspeicherort lautet /var/media. Die MP4-Dateien sollten im Verzeichnis angezeigt werden.

1. Verwenden Sie [Secure Copy (SCP)](../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md), um die Dateien auf Ihren lokalen Computer zu kopieren.
1. Geben Sie die Dateien mit dem [VLC Media Player](https://www.videolan.org/vlc/) oder einem anderen MP4-Player wieder.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie auch die anderen Schnellstartanleitungen durcharbeiten möchten, sollten Sie die von Ihnen erstellten Ressourcen beibehalten. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie diese Schnellstartanleitung ausgeführt haben, und löschen Sie dann alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

- Arbeiten Sie die Schnellstartanleitung zum [Ausführen von Azure Video Analyzer mit Ihrem eigenen Modell](analyze-live-video-use-your-model-http.md) durch. Darin ist beschrieben, wie KI auf Livevideofeeds angewendet wird.
- Zusätzliches für fortgeschrittene Benutzer:

  - Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
  - Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle einer Linux-VM in Azure. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Befolgen Sie die Anleitung unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true). Befolgen Sie anschließend die Anleitung unter [Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md?view=iotedge-2020-11&preserve-view=true), um das Gerät für Azure IoT Hub zu registrieren.
