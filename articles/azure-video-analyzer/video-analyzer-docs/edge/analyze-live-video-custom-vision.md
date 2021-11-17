---
title: Erste Schritte mit Azure Video Analyzer
description: In diesem Tutorial wird Schritt für Schritt beschrieben, wie Sie Livevideos mit Azure Video Analyzer in IoT Edge and Azure Custom Vision analysieren.
ms.topic: tutorial
ms.date: 06/01/2021
zone_pivot_groups: video-analyzer-programming-languages
ms.custom: ignite-fall-2021
ms.openlocfilehash: e87b1f70df4864a82f13b9e30cf93c2c5ca9eb14
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484267"
---
# <a name="tutorial-analyze-live-video-with-azure-video-analyzer-on-iot-edge-and-azure-custom-vision"></a>Tutorial: Analysieren von Livevideos mit Azure Video Analyzer in IoT Edge und Azure Custom Vision

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial erfahren Sie, wie Sie mit [Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) ein Containermodell erstellen, mit dem ein Spielzeug-LKW erkannt wird, und mithilfe der [KI-Erweiterbarkeitsfunktion](../analyze-live-video-without-recording.md#analyzing-video-using-a-custom-vision-model) von Azure Video Analyzer in Azure IoT Edge das Modell im Edgebereich bereitstellen, um in einem Livevideostream Spielzeug-LKW zu erkennen.

Wir zeigen Ihnen, wie Sie die Leistungsfähigkeit von Custom Vision nutzen, um ein Modell für maschinelles Sehen zu erstellen und zu trainieren, indem Sie einige Bilder heraufladen und mit Bezeichnungen versehen. Sie benötigen keinerlei Wissen über Data Science, maschinelles Lernen oder KI. Außerdem lernen Sie die Funktionen von Video Analyzer kennen, und es wird beschrieben, wie Sie auf einfache Weise ein benutzerdefiniertes Modell als Container auf dem Edgecomputer bereitstellen und einen simulierten Livevideofeed analysieren können.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-custom-vision/python/header.md)]
::: zone-end

Das Tutorial veranschaulicht folgende Vorgehensweisen:

- Einrichten der relevanten Ressourcen
- Erstellen eines Custom Vision-Modells in der Cloud, um Spielzeug-LKW zu erkennen und auf Edge-Ebene bereitzustellen.
- Erstellen und Bereitstellen einer Pipeline mit einer HTTP-Erweiterung für ein Custom Vision-Modell
- Ausführen des Beispielcodes
- Überprüfen und Interpretieren der Ergebnisse

Wenn Sie kein [Azure-Abonnement](../../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), bevor Sie beginnen.

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

Lesen Sie die folgenden Artikel, bevor Sie beginnen:

- [Video Analyzer in IoT Edge: Übersicht](../overview.md)
- [Übersicht über Azure Custom Vision](../../../cognitive-services/custom-vision-service/overview.md) (in englischer Sprache)
- [Video Analyzer in IoT Edge: Terminologie](../terminology.md)
- [Pipelinekonzept](../pipeline.md)
- [Video Analyzer ohne Videoaufzeichnung](../analyze-live-video-without-recording.md)
- [Tutorial: Entwickeln eines IoT Edge-Moduls](../../../iot-edge/tutorial-develop-for-linux.md)
- [Vorgehensweise beim Bearbeiten von „deployment.*.template.json“](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Voraussetzungen

* [Docker-Installation](https://docs.docker.com/desktop/#download-and-install) auf Ihrem Gerät

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-custom-vision/python/prerequisites.md)]
::: zone-end


## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

In diesem Tutorial wird mit einer [Spielzeugauto-Rückschlussvideodatei](https://avamedia.blob.core.windows.net/public/t2.mkv) ein Livestream simuliert. Sie können das Video z. B. in der Anwendung [VLC Media Player](https://www.videolan.org/vlc/) überprüfen. Wählen Sie **STRG+N** aus, und fügen Sie dann einen Link zum [Spielzeugauto-Rückschlussvideo](https://avamedia.blob.core.windows.net/public/t2.mkv) ein, um die Wiedergabe zu starten. Beachten Sie während der Wiedergabe des Videos, dass am 36-Sekunden-Marker ein Spielzeug-LKW im Video erscheint. Das benutzerdefinierte Modell wurde zum Erkennen dieses speziellen Spielzeug-LKW trainiert.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LPwK]

In diesem Tutorial verwenden Sie Video Analyzer in IoT Edge, um Spielzeug-LKW zu erkennen und entsprechende Rückschlussereignisse auf dem IoT Edge-Hub zu veröffentlichen.

## <a name="overview"></a>Überblick

![Diagramm, das eine Übersicht zu Custom Vision zeigt.](./media/custom-vision/topology-custom-vision.svg)

In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](../pipeline.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des [HTTP-Erweiterungsprozessors](../pipeline.md#http-extension-processor).

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Dabei werden die eingehenden Videoframes, die durch das Feld `samplingOptions` festgelegt wurden, abgetastet und darüber hinaus in den angegebenen Bildtyp umgewandelt. Anschließend wird das Bild an das Erkennungsmodell für Spielzeug-LKW weitergeleitet, das mit Custom Vision erstellt wurde. Der Knoten des HTTP-Erweiterungsprozessors erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse im Knoten der [Azure IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink), von dem diese Ereignisse an den [IoT Edge-Hub](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub) gesendet werden.

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Erstellen und Bereitstellen eines Custom Vision-Spielzeugerkennungsmodells

Wie der Name besagt, können Sie mit Custom Vision eine eigene benutzerdefinierte Objekterkennung oder -klassifizierung in der Cloud erstellen. Es bietet eine einfache, benutzerfreundliche und intuitive Oberfläche zum Erstellen von Custom Vision-Modellen, die über Container in der Cloud oder auf Edge-Ebene bereitgestellt werden können.

Zum Erstellen einer Erkennung für Spielzeug-LKW befolgen Sie die Schritte in [Schnellstart: Erstellen einer Objekterkennung mit der Custom Vision-Website](../../../cognitive-services/custom-vision-service/get-started-build-detector.md).

> [!IMPORTANT]
> Von diesem Custom Vision-Modul werden nur **Intel x86- und amd64-Architekturen** unterstützt. Überprüfen Sie die Architektur Ihres Edgegeräts, bevor Sie fortfahren.

Weitere Hinweise:

- Verwenden Sie für dieses Tutorial nicht die Beispielbilder, die im [Abschnitt „Voraussetzungen“](../../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) des Schnellstartartikels angegeben werden. Stattdessen haben wir einen bestimmten Satz Bilder verwendet, um ein Custom Vision Modell für die Spielzeugerkennung zu erstellen. Verwenden Sie [diese Bilder](https://avamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip), wenn Sie in der [Schnellstartanleitung](../../../cognitive-services/custom-vision-service/get-started-build-detector.md) aufgefordert werden, Ihre [Trainingsbilder](../../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) auszuwählen.
- Stellen Sie im Schnellstartartikel im Abschnitt zum Kennzeichnen von Bildern sicher, dass Sie den Spielzeug-LKW im Bild mit dem Tag „delivery truck“ versehen.
- Sicherstellen der Auswahl von „Allgemein (kompakt)“ als Option für „Domänen“ beim Erstellen des Custom Vision-Projekts

Nachdem Sie den Vorgang abgeschlossen haben, können Sie das Modell in einen Docker-Container exportieren, indem Sie die Schaltfläche **Exportieren** auf der Registerkarte **Performance** (Leistung) verwenden. Achten Sie darauf, Linux als Typ der Containerplattform auszuwählen. Auf dieser Plattform wird der Container ausgeführt. Der Computer, auf dem Sie den Container herunterladen, kann ein Computer mit Windows oder Linux sein. Die folgenden Anweisungen basieren auf der Containerdatei, die auf einen Windows-Computer heruntergeladen wurde.

![Bildschirm mit ausgewähltem Dockerfile.](./media/custom-vision/docker-file.png)

1. Sie sollten auf den lokalen Computer eine ZIP-Datei mit dem Namen `<projectname>.DockerFile.Linux.zip` heruntergeladen haben.
2. Überprüfen Sie, ob Docker installiert ist. Installieren Sie andernfalls [Docker](https://docs.docker.com/get-docker/) für Ihren Windows-Desktop.
3. Entpacken Sie die heruntergeladene Datei an einem Speicherort Ihrer Wahl. Verwenden Sie die Befehlszeile, um zum entzippten Ordnerverzeichnis zu wechseln. Die folgenden beiden Dateien sollten angezeigt werden: „app\labels.txt“ und „app\model.pb“
4. Klonen Sie das [Video Analyzer-Repository](https://github.com/Azure/video-analyzer), und navigieren Sie über die Befehlszeile zum Ordner „edge-modules\extensions\customvision\avaextension“.
5. Kopieren Sie die Dateien „labels.txt“ und „model.pb“ aus Schritt 3 in den Ordner „edge-modules\extensions\customvision\avaextension“. Gehen Sie in diesem Ordner wie folgt vor:

   Führen Sie die folgenden Befehle aus:

   1. `docker build -t cvtruck .`

      Mit diesem Befehl werden viele Pakete heruntergeladen, wird das Docker-Image erstellt und mit der Bezeichnung `cvtruck:latest` versehen.

      > [!NOTE]
      > Im Erfolgsfall sollten die folgenden Meldungen angezeigt werden: `Successfully built <docker image id>` und `Successfully tagged cvtruck:latest`. Falls bei den Buildbefehlen ein Fehler auftritt, versuchen Sie es noch einmal. Manchmal werden Abhängigkeitspakete im ersten Durchgang nicht heruntergeladen.
   2. `docker image ls`

      Mit diesem Befehl wird überprüft, ob sich das neue Bild in der lokalen Registrierung befindet.
   
## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

::: zone pivot="programming-language-csharp"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [setup development environment](./includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
::: zone-end

## <a name="examine-the-sample-files"></a>Untersuchen der Beispieldateien

::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/analyze-live-video-custom-vision/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generieren und Bereitstellen des Bereitstellungsmanifests

1. Navigieren Sie in Visual Studio Code zu „src/cloud-to-device-console-app/operations.json“.
2. Vergewissern Sie sich, dass unter `pipelineTopologySet` Folgendes zutrifft:<br/>
   `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/httpExtension/topology.json"`
3. Stellen Sie unter `livePipelineSet` Folgendes sicher:

   1. `"topologyName" : "InferencingWithHttpExtension"`
   2. Fügen Sie Folgendes oben zum Parameterarray hinzu: `{"name": "inferencingUrl","value": "http://cv/score"},`
   3. Ändern Sie den Wert von Parameter `rtspUrl` in `"rtsp://rtspsim:554/media/t2.mkv"`.
4. Stellen Sie unter `pipelineTopologyDelete` sicher, dass `"name": "InferencingWithHttpExtension"` ist.
5. Klicken Sie mit der rechten Maustaste auf die Datei „src/edge/deployment.customvision.template.json“, und wählen Sie dann **IoT Edge-Bereitstellungsmanifest generieren** aus.

   ![Screenshot, der das Generieren des IoT Edge-Bereitstellungsmanifests zeigt.](./media/custom-vision/deployment-template-json.png)

   Durch diese Aktion sollte im Ordner „src/edge/config“ die Manifestdatei „deployment.customvision.amd64.json“ erstellt werden.
6. Öffnen Sie die Datei „src/edge/ deployment.customvision.template.json“, und suchen Sie den JSON-Block `registryCredentials`. Dieser Block enthält die Adresse Ihrer Azure-Containerregistrierung sowie Ihren Benutzernamen und Ihr Kennwort.
7. Pushen Sie mit den folgenden Schritten an der Befehlszeile den lokalen Custom Vision-Container in Ihre Azure Container Registry-Instanz:

   1. Melden Sie sich mit dem folgenden Befehl bei der Registrierung an:

      `docker login <address>`

      Geben Sie den Benutzernamen und das Kennwort ein, wenn Sie zur Authentifizierung aufgefordert werden.

      > [!NOTE]
      > Das Kennwort ist in der Befehlszeile nicht sichtbar.
   2. Kennzeichnen Sie Ihr Image mithilfe des folgenden Befehls:<br/>
      `docker tag cvtruck <address>/cvtruck`.
   3. Pushen Sie Ihr Image, indem Sie diesen Befehl ausführen:<br/>
      `docker push <address>/cvtruck`.

      Bei Erfolg sollte `Pushed` in der Befehlszeile zusammen mit der SHA für das Image angezeigt werden.
   4. Sie können stattdessen auch die Azure Container Registry-Instanz im Azure-Portal überprüfen. Hier sehen Sie den Namen des Repositorys zusammen mit dem Tag.
8. Legen Sie die IoT Hub-Verbindungszeichenfolge fest, indem Sie im Bereich **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** auswählen. Sie können die Zeichenfolge aus der Datei „appsettings.json“ kopieren. (Hier finden Sie eine weitere empfohlene Vorgehensweise, um unter Verwendung des [Befehls zum Auswählen von IoT Hub](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub) sicherzustellen, dass in Visual Studio Code der richtige IoT-Hub konfiguriert ist.)

   ![Screenshot, der das Festlegen der IoT-Hub-Verbindungszeichenfolge zeigt.](./media/custom-vision/connection-string.png)
9. Klicken Sie dann mit der rechten Maustaste auf „src/edge/config/deployment.customvision.amd64.json“, und wählen Sie **Bereitstellung für einzelnes Gerät erstellen** aus.

   ![Screenshot, der das Erstellen der Bereitstellung für ein Einzelgerät zeigt.](./media/custom-vision/deployment-amd64-json.png)
10. Sie werden anschließend aufgefordert, ein IoT-Hub-Gerät auszuwählen. Wählen Sie in der Dropdownliste die Option **ava-sample-iot-edge-device** aus.
11. Aktualisieren Sie nach ungefähr 30 Sekunden den Azure IoT-Hub im unteren linken Bereich. Die folgenden Module sollten im Edge-Gerät als bereitgestellt angezeigt werden:

    - Edge-Hub (Modulname **edgeHub**)
    - Edge-Agent (Modulname **edgeAgent**)
    - Video Analyzer (Modulname **avaedge**)
    - Der RTSP-Simulator (Modul mit dem Namen **rtspsim**, das einen RTSP-Server als Quelle eines Livevideofeeds simuliert)
    - Custom Vision (Modul mit dem Namen **cv**, das auf dem Modell zur Erkennung des Spielzeug-LKW basiert)

Aus diesen Schritten wurde das Custom Vision-Modul hinzugefügt. 

## <a name="run-the-sample-program"></a>Ausführen des Beispielprogramms

Wenn Sie die Topologie für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `inferencingUrl` auf `http://cv/score` festgelegt wurde. Diese Einstellung bedeutet, dass der Rückschlussserver nach dem Erkennen von Spielzeug-LKW im Livevideo Ergebnisse zurückgibt, falls welche vorhanden sind.

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder wählen Sie **STRG+UMSCHALT+X** aus), und suchen Sie nach „Azure IoT-Hub“.
2. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.

   ![Screenshot von „Erweiterungseinstellungen“.](./media/custom-vision/extensions-tab.png)
3. Suchen Sie **Show Verbose Message** (Ausführliche Meldung anzeigen), und aktivieren Sie es.

   ![Screenshot von „Ausführliche Meldung anzeigen“.](./media/custom-vision/show-verbose-message.png)
4.  ::: zone pivot="programming-language-csharp"
    [!INCLUDE [header](includes/common-includes/csharp-run-program.md)]
    ::: zone-end

    ::: zone pivot="programming-language-python"
    [!INCLUDE [header](includes/common-includes/python-run-program.md)]
    ::: zone-end  

5. Der Code von operations.json beginnt mit Aufrufen der direkten Methoden `livePipelineList` und `livePipelineList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die **EINGABETASTE**, um den Vorgang fortzusetzen.

   Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

   - Ein Aufruf von `pipelineTopologySet`, der die vorherige `pipelineTopologyUrl` verwendet
   - Ein Aufruf von `livePipelineSet`, der den folgenden Textkörper verwendet:

   ```
        {
          "@apiVersion": "1.1",
          "name": "Sample-Pipeline-1",
          "properties": {
            "topologyName": "InferencingWithHttpExtension",
            "description": "Sample pipeline description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv/score"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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

   - Ein Aufruf von `livePipelineActivate`, mit dem die Pipeline und der Videodatenfluss aktiviert werden
   - Ein zweiter Aufruf von `livePipelineList`, um anzuzeigen, dass die Pipeline aktiv ist

6. Die Ausgabe im **Terminalfenster** wird an einer Aufforderung **Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen** angehalten. Warten Sie noch mit dem Drücken der **EINGABETASTE**. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
7. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Nachrichten angezeigt, die vom „Video Analyzer in IoT Edge“-Modul an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieses Tutorials sind diese Meldungen beschrieben.
8. Die Pipeline wird weiterhin ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden der Pipeline zurück zum **Terminalfenster**, und drücken Sie die **EINGABETASTE**. Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:

   - Mit dem Aufruf von `livePipelineDeactivate` wird die Pipeline deaktiviert.
   - Mit dem Aufruf von `livePipelineDelete` wird die Pipeline gelöscht.
   - Mit dem Aufruf von `pipelineTopologyDelete` wird die Topologie gelöscht.
   - Ein abschließender Aufruf von `pipelineTopologyList` zeigt, dass die Liste leer ist.

## <a name="interpret-the-results"></a>Interpretieren der Ergebnisse

Wenn Sie die Pipeline ausführen, durchlaufen die Ergebnisse vom Knoten des HTTP-Erweiterungsprozessors den Knoten der IoT Hub-Nachrichtensenke und werden an den IoT-Hub gesendet. Die Meldungen, die im **Ausgabefenster** angezeigt werden, enthalten einen Hauptteil und einen Abschnitt `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden vom Video Analyzer-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Pipeline instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben.


```
[IoTHubMonitor] [9:42:18 AM] Message received from [avasample-iot-edge-device/avaedge]:
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

Beachten Sie in dieser Meldung diese Details:

- Bei der Meldung handelt es sich um ein Diagnoseereignis. `MediaSessionEstablished` zeigt an, dass der RTSP-Quellknoten (das Subjekt), mit dem RTSP-Simulator verbunden wurde und begonnen hat, einen simulierten Livefeed zu empfangen.
- In `properties` wird mit `subject` angegeben, dass die Nachricht über den Knoten der RTSP-Quelle in der Pipeline generiert wurde.
- In `properties` gibt der Ereignistyp an, dass es sich bei diesem Ereignis um ein Diagnoseereignis handelt.
- Der Ereigniszeitpunkt gibt die Uhrzeit an, zu der das Ereignis aufgetreten ist.
- Der Abschnitt „body“ enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der Knoten des HTTP-Erweiterungsprozessors empfängt Rückschlussergebnisse vom Custom Vision-Container und gibt sie über den Knoten der IoT Hub-Nachrichtensenke als Rückschlussereignisse aus.


```
{
  "body": {
    "timestamp": 145892470449324,
    "inferences": [
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.20541823
          },
          "box": {
            "l": 0.6826309,
            "t": -0.01415127,
            "w": 0.3135161,
            "h": 0.94683206
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.14967085
          },
          "box": {
            "l": 0.33310884,
            "t": 0.03174839,
            "w": 0.13532706,
            "h": 0.54967254
          }
        }
      },
      {
        "type": "entity",
        "entity": {
          "tag": {
            "value": "delivery truck",
            "confidence": 0.1352181
          },
          "box": {
            "l": 0.48884687,
            "t": 0.44746214,
            "w": 0.025887,
            "h": 0.05414263
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/...",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/httpExtension",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-14T21:24:09.436Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637563926153483223",
    "iothub-enqueuedtime": 1621027452077,
    "iothub-message-source": "Telemetry",
    "messageId": "96f7f0b5-728d-4e3e-a7bb-4e3198c58726",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
```

Beachten Sie die folgenden Informationen in den vorangehenden Meldungen:

- Mit „subject“ in `properties` wird auf den Knoten in der Pipeline verwiesen, von dem die Nachricht generiert wurde. In diesem Fall stammt die Meldung vom Prozessor der HTTP-Erweiterung.
- Der „eventType“ in `properties` gibt an, dass es sich um ein Analyserückschlussereignis handelt.
- Der Ereigniszeitpunkt gibt die Uhrzeit an, zu der das Ereignis aufgetreten ist.
- Der Textkörper enthält Daten zu dem Analyseereignis. In diesem Fall ist das Ereignis ein Rückschlussereignis. Daher enthält der Textkörper ein Array von Rückschlüssen, die als „predictions“ (Vorhersagen) bezeichnet werden.
- Der Abschnitt „inferences“ enthält eine Liste mit den Vorhersagen dazu, wo sich ein Spielzeug-LKW (Tag „delivery truck“) im Frame befindet. Sie werden sich erinnern – „delivery truck“ ist der benutzerdefinierte Tag, den Sie Ihrem benutzerdefinierten trainierten Modell für den Spielzeug-LKW zur Verfügung gestellt haben. Das Modell führt Rückschlüsse auf den Spielzeug-LKW im Eingabevideo durch und identifiziert ihn mit verschiedenen Konfidenzwerten für die Wahrscheinlichkeit.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die anderen Tutorials und Schnellstartanleitungen durcharbeiten möchten, behalten Sie die von Ihnen erstellten Ressourcen. Wechseln Sie andernfalls zum Azure-Portal, navigieren Sie zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial durchgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

- Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
- Verwenden Sie ein AMD64- oder x64-Linux-Gerät anstelle eines virtuellen Azure-Linux-Computers. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../../iot-edge/how-to-install-iot-edge.md) befolgen.

Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../../iot-edge/quickstart-linux.md) ausführen.  
