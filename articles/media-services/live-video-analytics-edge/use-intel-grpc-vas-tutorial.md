---
title: Analysieren von Livevideos mithilfe der Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ über gRPC mit Live Video Analytics
description: In diesem Tutorial erfahren Sie, wie Sie Live Video Analytics mit der Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ von Intel verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren.
ms.topic: tutorial
ms.date: 02/04/2021
ms.service: media-services
ms.author: faneerde
author: fvneerden
ms.openlocfilehash: f3498175030be0c6bc7b57db25dece5f8c2d83ea
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626834"
---
# <a name="tutorial-analyze-live-video-by-using-live-video-analytics-with-intel-openvino-dl-streamer--edge-ai-extension"></a>Tutorial: Analysieren von Livevideos mithilfe von Live Video Analytics und der Edge-KI-Erweiterung „Intel OpenVINO™ DL Streamer“ 

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

In diesem Tutorial erfahren Sie, wie Sie die Edge AI Extension „Intel OpenVINO™ DL Streamer“ von Intel verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Außerdem wird veranschaulicht, wie dieser Rückschlussserver Ihnen Zugriff auf diverse Modelle zum Erkennen von Objekten (z. B. eine Person, ein Fahrzeug oder ein Fahrrad) oder für die Objektklassifizierung (z. B. Fahrzeugzuordnungen) sowie ein Modell für die Objektnachverfolgung (Personen, Fahrzeuge und Fahrräder). Die Integration mit dem gRPC-Modul ermöglicht Ihnen das Senden von Videoframes an den KI-Rückschlussserver. Die Ergebnisse werden dann an den IoT Edge-Hub gesendet. Wenn Sie diesen Rückschlussdienst auf demselben Computeknoten wie Live Video Analytics ausführen, können Sie Videodaten über gemeinsam genutzten Speicher senden. Dies ermöglicht Ihnen die Ausführen von Rückschlüssen in der Geschwindigkeit der Bildfrequenz des Livevideofeeds (z. B. 30 Frames pro Sekunde). 

In diesem Tutorial werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet. Es basiert auf Beispielcode, der in C# geschrieben ist, und baut auf der Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) auf.

> [!NOTE]
> Für dieses Tutorial muss ein x86-64-Computer als Edgegerät verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto das ein aktives Abonnement beinhaltet. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
  > [!NOTE]
  > Sie benötigen ein Azure-Abonnement mit Berechtigungen zum Erstellen von Dienstprinzipalen. (Die **Rolle „Besitzer“** stellt diese Berechtigungen bereit.) Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt. 
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Sollten Sie die Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) nicht durchlaufen haben, führen Sie die Schritte zum [Einrichten der Azure-Ressourcen](detect-motion-emit-events-quickstart.md#set-up-azure-resources) aus.

> [!TIP]
> Bei der Installation von Azure IoT Tools werden Sie unter Umständen aufgefordert, Docker zu installieren. Sie können diese Aufforderung ignorieren.

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Beim Einrichten der Azure-Ressourcen wird ein kurzes Parkplatzvideo auf den virtuellen Linux-Computer in Azure kopiert, den Sie als IoT Edge-Gerät verwenden. In dieser Schnellstartanleitung wird die Videodatei verwendet, um einen Livestream zu simulieren.

Öffnen Sie eine Anwendung wie etwa den [VLC Media Player](https://www.videolan.org/vlc/). Drücken Sie STRG+N, und fügen Sie dann einen Link zum [Video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) ein, um die Wiedergabe zu starten. Das Video zeigt Fahrzeuge auf einem Parkplatz. Die meisten davon sind geparkt. Ein einzelnes Fahrzeug bewegt sich.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

In diesem Schnellstart verwenden Sie Live Video Analytics in IoT Edge gemeinsam mit der Edge AI Extension „Intel OpenVINO™ DL Streamer“, um Objekte wie Fahrzeuge, Personen oder Fahrräder zu erkennen, Fahrzeuge zu klassifizieren oder nachzuverfolgen. Die resultierenden Rückschlussereignisse werden im IoT Edge-Hub veröffentlicht.

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-intel-openvino-tutorial/grpc-vas-extension-with-vino.svg" alt-text="Übersicht über LVA MediaGraph":::

In diesem Diagramm ist der Fluss der Signale in diesem Schnellstart dargestellt. Ein [Edge-Modul](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Ein [RTSP-Quellknoten](media-graph-concept.md#rtsp-source) bezieht den Videofeed von diesem Server und sendet Videoframes an den Knoten des [gRPC-Erweiterungsprozessors](media-graph-concept.md#grpc-extension-processor). 

Dieser gRPC-Erweiterungsprozessor verwendet decodierte Videoeinzelbilder als Eingabe und leitet diese an einen [gRPC](terminology.md#grpc)-Endpunkt weiter, der von einem gRPC-Server zur Verfügung gestellt wird. Der Knoten unterstützt das Übertragen von Daten mithilfe von [Shared Memory](https://en.wikipedia.org/wiki/Shared_memory) oder durch direktes Einbetten der Inhalte in den Text von gRPC-Nachrichten. Darüber hinaus weist der Knoten einen integrierten Bildformatierer auf, um Videoeinzelbilder zu skalieren und zu codieren, bevor sie an den gRPC-Endpunkt weitergeleitet werden. Der Skalierer verfügt über Optionen für das Beibehalten, Auffüllen oder Strecken des Seitenverhältnisses. Der Bildcodierer unterstützt die Formate JPEG, PNG und BMP. Weitere Informationen zum Prozessor finden Sie [hier](media-graph-extension-concept.md#grpc-extension-processor).

Inhalt des Tutorials:

1. Bereitstellen des Mediengraphen
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="about-intel-openvino-dl-streamer--edge-ai-extension-module"></a>Informationen zum Edge AI Extension-Modul „OpenVINO™ DL Streamer“


Das Edge AI Extension-Modul „OpenVINO™ DL Streamer“ ist ein Microservice, der auf Video Analytics Serving (VA Serving) von Intel basiert. Dabei handelt es sich um eine Anwendung, die Videoanalysepipelines mit Daten versorgt, die mit OpenVINO™ DL Streamer erstellt wurden. Entwickler können entschlüsselte Videoframes an das Edge AI Extension-Modul senden, das die Erkennung, Klassifizierung oder Nachverfolgung durchführt und dann die Ergebnisse zurückgibt. Das Edge AI Extension-Modul stellt gRPC-APIs zur Verfügung, die mit Videoanalyseplattformen wie Live Video Analytics in IoT Edge von Microsoft kompatibel sind. 

Zur Erstellung komplexer Hochleistungslösungen für die Livevideoanalyse empfiehlt es sich, Live Video Analytics im IoT Edge-Modul mit einer leistungsfähigen Rückschluss-Engine zu kombinieren, die das Skalierungspotential am Edge nutzen kann. In diesem Tutorial werden Rückschlussanforderungen an die [Edge AI Extension „Intel OpenVINO™ DL Streamer“]() gesendet. Dabei handelt es sich um ein Edge-Modul, das für die Zusammenarbeit mit Live Video Analytics in IoT Edge konzipiert wurde.

Das erste Release dieses Rückschlussservers bietet Zugriff auf folgende [Modelle](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options):

- „object_detection“ für „person_vehicle_bike_detection“ ![Objekterkennung für Fahrzeuge](./media/use-intel-openvino-tutorial/object-detection.png)

- „object_classification“ für „vehicle_attributes_recognition“ ![Objektklassifizierung für Fahrzeuge](./media/use-intel-openvino-tutorial/object-classification.png)

- „object_tracking“ für „person_vehicle_bike_tracking“ ![Objektnachverfolgung für Personenfahrzeuge](./media/use-intel-openvino-tutorial/object-tracking.png)

Für den schnellen Start werden vorab geladene Objekterkennungs-, Objektklassifizierungs- und Objektnachverfolgungspipelines verwendet. Darüber hinaus sind die vorab geladenen Modelle [person-vehicle-bike-detection-crossroad-0078](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/person-vehicle-bike-detection-crossroad-0078/README.md) und [vehicle-attributes-recognition-barrier-0039](https://github.com/openvinotoolkit/open_model_zoo/blob/master/models/intel/vehicle-attributes-recognition-barrier-0039/README.md) enthalten.

> [!NOTE]
> Wenn Sie das Edge AI Extension-Modul „OpenVINO™ DL Streamer“ von Intel und die enthaltene Software herunterladen und verwenden, stimmen Sie den gemäß der [Lizenzvereinbarung](https://www.intel.com/content/www/us/en/legal/terms-of-use.html) geltenden Geschäftsbedingungen zu.
> Intel setzt sich für die Achtung der Menschenrechte ein und ist darauf bedacht, eine Mitschuld an Menschenrechtsverletzungen zu vermeiden. Weitere Informationen finden Sie in den [globalen Menschenrechtsprinzipien von Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). Produkte und Software von Intel dürfen nur in Anwendungen verwendet werden, die kein international anerkanntes Menschenrecht verletzen oder zu einer solchen Verletzung beitragen.

Sie können die Flexibilität der verschiedenen Pipelines für Ihren speziellen Anwendungsfall verwenden, indem Sie einfach die Umgebungsvariablen für die Pipeline in Ihrer Bereitstellungsvorlage anpassen. Dies ermöglicht es Ihnen, schnell das Pipelinemodell zu wechseln. Darüber hinaus dauert das Ändern der Medienpipeline und des Rückschlussmodells bei Kombination mit Live Video Analytics nur wenige Sekunden.  

## <a name="create-and-deploy-the-media-graph"></a>Erstellen und Bereitstellen des Mediengraphen

### <a name="examine-and-edit-the-sample-files"></a>Untersuchen und Bearbeiten der Beispieldateien

Als Teil der Voraussetzungen haben Sie den Beispielcode in einen Ordner heruntergeladen. Führen Sie die unten angegebenen Schritte aus, um die Beispieldateien zu überprüfen und zu bearbeiten.

1. Navigieren Sie in Visual Studio Code zu *src/edge*. Ihre *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.

    Die Bereitstellungsvorlage verweist auf das Bereitstellungsmanifest für das Edge-Gerät. Es enthält einige Platzhalterwerte. Die *ENV*-Datei enthält die Werte für diese Variablen.

1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin finden Sie Ihre Datei *appsettings.json* und einige andere Dateien:

    * ***c2d-console-app.csproj***: Die Projektdatei für Visual Studio Code.
    * ***operations.json***: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    * ***Program.cs***: Das Beispiel für den Programmcode. Für diesen Code gilt Folgendes:

        * Laden der App-Einstellungen
        * Aufrufen direkter Methoden, die vom Modul „Live Video Analytics in IoT Edge“ verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen [direkten Methoden](direct-methods.md) aufrufen.
        * Anhalten der Ausführung, sodass Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
        * Aufrufen direkter Methoden zur Bereinigung der Ressourcen


1. Bearbeiten Sie die Datei *operations.json*:
    * Ändern Sie den Link zur Graphtopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json"`

    * Bearbeiten Sie unter `GraphInstanceSet` den Namen der Graphtopologie, damit er mit dem Wert im vorherigen Link übereinstimmt:

      `"topologyName" : "InferencingWithOpenVINOgRPC"`

    * Ändern Sie unter `GraphTopologyDelete` den Namen:

      `"name": "InferencingWithOpenVINOgRPC"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests

1. Klicken Sie mit der rechten Maustaste auf die Datei *src/edge/deployment.openvino.grpc.cpu.template.json*, und wählen Sie dann **Generate IoT Edge Deployment Manifest** (IoT Edge-Bereitstellungsmanifest generieren) aus.

    ![Generate IoT Edge Deployment Manifest (IoT Edge-Bereitstellungsmanifest generieren)](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Die Manifestdatei *deployment.openvino.grpc.cpu.amd64.json* wird im Ordner *src/edge/config* erstellt.

> [!NOTE]
> Außerdem ist die Vorlage *deployment.openvino.grpc.gpu.template.json* enthalten, die die GPU-Unterstützung für das Edge AI Extension-Modul „OpenVINO™ DL Streamer“ von Intel aktiviert. Diese Vorlagen verweisen auf das Docker-Hub-Image von Intel.

Die oben genannten Vorlagen verweisen auf das Docker-Hub-Image von Intel. Wenn Sie lieber eine Kopie Ihrer eigenen Azure Container Registry-Instanz hosten möchten, können Sie die unten aufgeführten Schritte 1 und 2 ausführen:
1. Melden Sie sich per SSH bei einem Gerät mit installierten Docker-CLI-Tools (Ihr Edgegerät) an, und pullen/kennzeichnen/pushen Sie den Container mithilfe der folgenden drei Schritte:
    * Pullen Sie das Intel-Image aus Docker Hub:

        `sudo docker pull intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`
    
    * Kennzeichnen Sie das Intel-Image mit dem Namen Ihrer eigenen Azure Container Registry-Instanz. Ersetzen Sie {YOUR ACR NAME} durch den Namen Ihrer ACR-Instanz, den Sie in der ENV-Datei finden:

        `sudo docker image tag intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
    * Pushen Sie Ihr gekennzeichnetes Image an Ihre Azure Container Registry-Instanz:

        `sudo docker push {YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    
2. Als Nächstes müssen Sie die Vorlagen bearbeiten, um auf Ihr neues in Azure Container Registry gehostetes Image zu verweisen.
    * Klicken Sie mit der rechten Maustaste auf die Datei *deployment.openvino.grpc.cpu.template.json*, navigieren Sie zum Abschnitt *lavExtension* des Moduls, und ersetzen Sie Folgendes:

        `intel/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension`

        Durch:

        `{YOUR ACR NAME/video-analytics-serving:0.4.1-dlstreamer-edge-ai-extension}`
    * Wiederholen Sie Schritt 2 für die Datei *deployment.openvino.grpc.gpu.template.json*.


3. Überspringen Sie diesen Schritt, wenn Sie die Schnellstartanleitung [Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md) abgeschlossen haben. 

    Wählen Sie andernfalls in der Nähe des Bereichs **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** und dann **Festlegen der IoT Hub-Verbindungszeichenfolge** aus. Sie können die Zeichenfolge aus der Datei *appsettings.json* kopieren. Um sicherzustellen, dass Sie den richtigen IoT Hub in Visual Studio Code konfiguriert haben, verwenden Sie alternativ den Befehl [IoT Hub auswählen](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Festlegen der IoT Hub-Verbindungszeichenfolge](./media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Unter Umständen werden Sie aufgefordert, für die IoT Hub-Instanz die Informationen zum integrierten Endpunkt anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrer IoT Hub-Instanz navigieren und im linken Navigationsbereich nach der Option **Integrierte Endpunkte** suchen. Klicken Sie darauf, und suchen Sie im Abschnitt **Event Hub-kompatibler Endpunkt** nach dem **Event Hub-kompatiblen Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klicken Sie mit der rechten Maustaste auf *src/edge/config/deployment.openvino.grpc.cpu.template.json*, und wählen Sie die Option **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen) aus. 

    ![Create Deployment for Single Device (Bereitstellung für einzelnes Gerät erstellen)](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Wenn Sie aufgefordert werden, ein IoT Hub-Gerät auszuwählen, wählen Sie **lva-sample-device** aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden unten links im Fenster den Dienst „Azure IoT Hub“. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

    * Live Video Analytics-Modul mit dem Namen **lvaEdge**
    * Modul **rtspsim**, das einen RTSP-Server simuliert und als Quelle eines Livevideofeeds fungiert
    * Das Modul **IvaExtension**, bei dem es sich um die Edge AI Extension „OpenVINO™ DL Streamer“ von Intel handelt 

### <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

Klicken Sie mit der rechten Maustaste auf das Live Video Analytics-Gerät, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus. Sie müssen diesen Schritt ausführen, um die IoT Hub-Ereignisse im **Ausgabefenster** von Visual Studio Code zu überwachen. 

![Starten der Überwachung](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles-persons-or-bike"></a>Ausführen des Beispielprogramms zum Erkennen von Fahrzeugen/Personen/Fahrrädern
Wenn Sie die [Graphtopologie](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) für dieses Tutorial in einem Browser öffnen, sehen Sie, dass der Wert von `grpcExtensionAddress` auf `tcp://lvaExtension:5001` festgelegt wurde. Im Vergleich zum *httpExtensionOpenVINO*-Beispiel müssen Sie keine Änderung an der URL zum gRPC-Server vornehmen. Stattdessen weisen Sie das Modul wie zuvor beschrieben mithilfe der Umgebungsvariablen an, eine spezifische Pipeline auszuführen. In der Standardvorlage ist „object_detection“ für „person_vehicle_bike_detection“ festgelegt. Sie können jedoch auch mit anderen unterstützten Pipelines experimentieren. 

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG + UMSCHALT + X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, um das Kontextmenü zu öffnen, und wählen Sie **Erweiterungseinstellungen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Erweiterungseinstellungen":::
1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Drücken Sie die F5-Taste, um eine Debugsitzung zu starten. Es werden dann Nachrichten im **Terminalfenster** ausgegeben.
1. Der Code von *operations.json* beginnt mit Aufrufen der direkten Methoden `GraphTopologyList` und `GraphInstanceList`. Wenn Sie nach dem Durcharbeiten vorheriger Schnellstartanleitungen eine Ressourcenbereinigung durchgeführt haben, werden bei diesem Prozess leere Listen zurückgegeben, und anschließend wird die Ausführung angehalten. Drücken Sie die EINGABETASTE, um den Vorgang fortzusetzen.

    Im **Terminalfenster** werden die nächsten Aufrufe direkter Methoden angezeigt:

     * Ein Aufruf von `GraphTopologySet`, der die vorherige `topologyUrl` verwendet
     * Ein Aufruf von `GraphInstanceSet`, der den folgenden Textkörper verwendet:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINOgRPC",
             "description": "Sample graph description",
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
               }
             ]
           }
         }
         ```

     * Ein Aufruf von `GraphInstanceActivate`, der die Graphinstanz und den Videodatenfluss startet
     * Zweiter Aufruf von `GraphInstanceList` mit der Anzeige, dass sich die Graphinstanz im ausgeführten Zustand befindet
1. Die Ausgabe im **Terminalfenster** wird mit der Aufforderung `Press Enter to continue` angehalten. Warten Sie noch mit dem Drücken der EINGABETASTE. Scrollen Sie nach oben, um die JSON-Antwortnutzlasten für die aufgerufenen direkten Methoden anzuzeigen.
1. Wechseln Sie in Visual Studio Code zum **Ausgabefenster**. Es werden Meldungen angezeigt, die vom Modul „Live Video Analytics in IoT Edge“ an den IoT-Hub gesendet werden. Im folgenden Abschnitt dieser Schnellstartanmeldung sind diese Meldungen beschrieben.
1. Der Mediengraph wird weiter ausgeführt, und es werden Ergebnisse ausgegeben. Der RTSP-Simulator führt das Quellvideo als Schleife aus. Wechseln Sie zum Beenden des Mediengraphs zurück zum **Terminalfenster**, und drücken Sie die EINGABETASTE. 

    Mit den nächsten Aufrufen wird die Ressourcenbereinigung durchgeführt:
      * Mit dem Aufruf von `GraphInstanceDeactivate` wird die Graphinstanz deaktiviert.
      * Mit dem Aufruf von `GraphInstanceDelete` wird die Instanz gelöscht.
      * Mit dem Aufruf von `GraphTopologyDelete` wird die Topologie gelöscht.
      * Ein abschließender Aufruf von `GraphTopologyList` zeigt, dass die Liste leer ist.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

Wenn Sie den Mediengraph ausführen, werden die Ergebnisse aus dem Knoten des HTTP-Erweiterungsprozessors über den Knoten der IoT Hub-Senke an den IoT-Hub übergeben. Die im **Ausgabefenster** angezeigten Meldungen enthalten jeweils die Abschnitte `body` und `applicationProperties`. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Meldungen werden vom Live Video Analytics-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert. 

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn ein Mediengraph instanziiert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben. Der Ereignistyp lautet **Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished**.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1612432131600584 1 IN IP4 172.18.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/homes_00425.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-214.166\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/homes_00425.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=64001F;sprop-parameter-sets=Z2QAH6zZQFAFuwFsgAAAAwCAAAAeB4wYyw==,aOvhEsiw\r\na=control:track1\r\n"
}
```

Beachten Sie in dieser Meldung diese Details:

* Bei der Meldung handelt es sich um ein Diagnoseereignis. `MediaSessionEstablished` gibt an, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
* In `applicationProperties` wird mit `subject` angegeben, dass die Meldung über den Knoten der RTSP-Quelle im Mediengraph generiert wurde.
* In `applicationProperties` wird mit `eventType` angegeben, dass es sich um ein Diagnoseereignis handelt.
* Die `eventTime` gibt den Zeitpunkt des Ereignisses an.
* Der `body` enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Ereignis „Inference“

Der gRPC-Erweiterungsprozessorknoten empfängt Rückschlussergebnisse von der Edge AI Extension „OpenVINO™ DL Streamer“ von Intel. Anschließend gibt er die Ergebnisse über den Knoten der IoT Hub-Senke als Rückschlussereignisse aus. 

In diesen Ereignissen ist der Typ auf `entity` festgelegt, um anzuzeigen, dass es sich um eine Entität wie etwa ein Auto oder einen LKW handelt. Der Wert von `eventTime` ist die UTC-Uhrzeit, zu der das Objekt erkannt wurde. 

Im folgenden Beispiel wird gezeigt, dass ein Fahrzeug mit der Art von Fahrzeug („Van“) und der Farbe („White“) sowie einem Konfidenzwert über „0,9“ identifiziert wurde. Außerdem wurde der Entität eine ID zugewiesen, wenn das Modell für die Objektnachverfolgung verwendet wird.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "timestamp": 145118912223221,
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": {
          "value": "vehicle",
          "confidence": 0.9605301
        },
        "attributes": [
          {
            "name": "color",
            "value": "white",
            "confidence": 0.9605301
          },
          {
            "name": "type",
            "value": "car",
            "confidence": 0.9605301
          }
        ],
        "box": {
          "l": 0.3958135,
          "t": 0.078730375,
          "w": 0.48403296,
          "h": 0.94352424
        },
        "id&quot;: &quot;1"
      }
    }
}
```

Beachten Sie in den Meldungen die folgenden Details:

* In `applicationProperties` wird von `subject` auf den Knoten in der Graphtopologie verwiesen, von dem die Meldung generiert wurde. 
* In `applicationProperties` wird unter `eventType` darauf hingewiesen, dass es sich hierbei um ein Analyseereignis handelt.
* Der Wert `eventTime` ist die Uhrzeit, zu der das Ereignis eingetreten ist.
* Der Abschnitt `body` enthält Daten zum Analyseereignis. Da es sich in diesem Fall um ein Rückschlussereignis handelt, enthält der Text Daten vom Typ `inferences`.
* Im Abschnitt `inferences` ist angegeben, dass `type` auf `entity` festgelegt ist. Dieser Abschnitt enthält zusätzliche Daten zur Entität.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Ausführen des Beispielprogramms für die Erkennung von Personen, Fahrzeugen oder Fahrrädern
Sie müssen die Bereitstellungsvorlage wechseln, um ein anderes Modell zu verwenden. Zum Wechseln zwischen den unterstützten Modellen können Sie die Umgebungsvariablen im IvaExtension-Modul ändern. Informationen zu den unterstützten Werten und Modellkombinationen finden Sie in diesem [Dokument auf GitHub](https://github.com/intel/video-analytics-serving/tree/master/samples/ava_ai_extension#edge-ai-extension-module-options).

```
"Env":[
"PIPELINE_NAME=object_classification",
"PIPELINE_VERSION=vehicle_attributes_recognition"
],
```
> [!TIP]
> Kopieren Sie die Vorlage, und speichern Sie sie mit einem neuen Namen für jede mögliche Pipeline. Auf diese Weise können Sie zwischen Modellen wechseln, indem Sie eine neue Bereitstellung anhand einer dieser Vorlagen erstellen.

Nachdem Sie die Variablen geändert haben, können Sie die Vorlage wieder auf dem Gerät bereitstellen. Nun können Sie die obigen Schritte wiederholen, um das Beispielprogramm mit der neuen Pipeline auszuführen. Die resultierenden Rückschlüsse sind ähnlich (im Schema), umfassen aber je nach ausgewähltem Pipelinemodell mehr oder weniger Informationen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie weitere Schnellstartanleitungen oder Tutorials ausprobieren möchten, behalten Sie die von Ihnen erstellten Ressourcen. Navigieren Sie andernfalls im Azure-Portal zu Ihren Ressourcengruppen, wählen Sie die Ressourcengruppe aus, unter der Sie dieses Tutorial ausgeführt haben, und löschen Sie alle Ressourcen.

## <a name="next-steps"></a>Nächste Schritte

Zusätzliches für fortgeschrittene Benutzer:

* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Verwenden Sie ein x64-Linux-Gerät anstelle einer Azure-Linux-VM. Dieses Gerät muss sich im gleichen Netzwerk befinden wie die IP-Kamera. Sie können die Anleitungen unter [Installieren der Azure IoT Edge-Runtime unter Linux](../../iot-edge/how-to-install-iot-edge.md) befolgen. Registrieren Sie das Gerät anschließend beim Azure IoT Hub, indem Sie die Anweisungen in [Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../../iot-edge/quickstart-linux.md) ausführen.
