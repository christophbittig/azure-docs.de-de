---
title: Analysieren von Livevideos mit maschinellem Sehen für die räumliche Analyse – Azure
description: In diesem Tutorial wird gezeigt, wie Sie Azure Video Analyzer zusammen mit dem KI-Feature „Maschinelles Sehen“ für die räumliche Analyse von Azure Cognitive Services verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren.
author: Juliako
ms.author: juliako
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 341c3df126d0ff949c3ba40e5d679bb2e3147ff6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488637"
---
# <a name="tutorial-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Tutorial: Analysieren von Livevideos mit maschinellem Sehen für die räumliche Analyse (Vorschau)

[!INCLUDE [header](includes/edge-env.md)]

In diesem Tutorial wird gezeigt, wie Sie Azure Video Analyzer zusammen mit dem [KI-Feature „Maschinelles Sehen“ für die räumliche Analyse von Azure Cognitive Services](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) verwenden, um einen Livevideofeed einer (simulierten) IP-Kamera zu analysieren. Es wird gezeigt, wie Sie mit diesem Rückschlussserver das Streamingvideo analysieren können, um räumliche Beziehungen zwischen Personen und Bewegung im physischen Raum zu ermitteln. Eine Teilmenge der Frames im Videofeed wird an den Rückschlussserver gesendet, und die Ergebnisse werden an den IoT Edge-Hub gesendet. Wenn bestimmte Bedingungen erfüllt sind, werden Videoclips aufgezeichnet und im Video Analyzer-Konto gespeichert.

In diesem Lernprogramm führen Sie folgende Schritte aus:

> [!div class="checklist"]
>
> - Einrichten von Ressourcen
> - Untersuchen des Codes
> - Ausführen des Beispiels
> - Überwachen von Ereignissen

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung

Lesen Sie diese Artikel, bevor Sie beginnen:

- [Azure Video Analyzer-Übersicht](../overview.md)
- [Video Analyzer-Terminologie](../terminology.md)
- [Pipelinekonzepte](../pipeline.md)
- [Ereignisbasierte Videoaufzeichnung](record-event-based-live-video.md)
- [Azure Cognitive Service-Container Maschinelles Sehen](../../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) für räumliche Analysen.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt sein, um das Modul „spatial-analysis“ mit dem Azure Video Analyzer-Modul zu verbinden.

[!INCLUDE [prerequisites](./includes/common-includes/csharp-prerequisites.md)]

   > [!Note]
   > Vergewissern Sie sich, dass für das Netzwerk, mit dem der Entwicklungscomputer verbunden ist, Advanced Message Queueing Protocol über Port 5671 zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

1. **Auswählen eines Computegeräts**  

    Zum Ausführen des Containers für räumliche Analyse benötigen Sie ein Computegerät mit einer [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). Es empfiehlt sich zwar, **[Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)** mit GPU-Beschleunigung zu verwenden, der Container kann jedoch auf jedem **Desktopcomputer** oder in jeder **Azure VM** verwendet werden, sofern auf dem Hostcomputer [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) installiert ist.

   #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)

   Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Eine ausführliche Anleitung zur Vorbereitung und Einrichtung finden Sie in der [Azure Stack Edge-Dokumentation](../../../databox-online/azure-stack-edge-deploy-prep.md).

   #### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)

   #### <a name="minimum-hardware-requirements"></a>Hardwaremindestanforderungen

   - 4 GB Systemarbeitsspeicher
   - 4 GB GPU-Arbeitsspeicher
   - CPU mit acht Kernen
   - 1 NVIDIA Tesla T4-GPU
   - 20 GB Festplattenspeicher

   #### <a name="recommended-hardware"></a>Empfohlene Hardware

   - 32 GB Systemarbeitsspeicher
   - 16 GB GPU-Arbeitsspeicher
   - CPU mit acht Kernen
   - 2 NVIDIA Tesla T4-GPUs
   - 50 GB SSD-Speicherplatz

   #### <a name="azure-vm-with-gpu"></a>[Azure-VM mit GPU](#tab/virtual-machine)

   Sie können einen [virtuellen Computer der NC-Serie](../../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mit einer K80-GPU verwenden.
        
1. **Einrichten des Edgegeräts**

    #### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)
    [Konfigurieren von Compute im Azure Stack Edge-Portal](../../../cognitive-services/computer-vision/spatial-analysis-container.md#configure-compute-on-the-azure-stack-edge-portal)
    #### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)
    [Befolgen Sie diese Anleitung, falls es sich bei Ihrem Hostcomputer nicht um ein Azure Stack Edge-Gerät handelt.](../../../cognitive-services/computer-vision/spatial-analysis-container.md#install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer)
    #### <a name="azure-vm-with-gpu"></a>[Azure-VM mit GPU](#tab/virtual-machine)
    1. [Erstellen Sie den virtuellen Computer](../../../cognitive-services/computer-vision/spatial-analysis-container.md?tabs=virtual-machine#create-the-vm), und installieren Sie die erforderlichen Docker auf dem virtuellen Computer

        > [!Important]
        > Überspringen Sie den Schritt für das **IoT-Bereitstellungsmanifest**, das im betreffenden Dokument genannt ist. Wir verwenden unsere eigene **[Bereitstellungsmanifest](#configure-deployment-template)** -Datei, um die erforderlichen Container bereitzustellen.

    1. Stellen Sie eine Verbindung mit Ihrem virtuellen Computer her, und geben Sie im Terminal den folgenden Befehl ein:
    ```bash
    bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"
    ```
    Das Azure Video Analyzer-Modul wird auf dem Edgegerät mit lokalen Benutzerkonten ohne Berechtigungen ausgeführt. Darüber hinaus werden bestimmte lokale Ordner benötigt, um die Daten für die Anwendungskonfiguration zu speichern. Abschließend nutzen wir für diese Schrittanleitung einen [RTSP-Simulator](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555), mit dem ein Videofeed in Echtzeit zur Analyse an ein AVA-Modul weitergeleitet wird. Für diesen Simulator werden als Eingabe vorab aufgezeichnete Videodateien aus einem Eingabeverzeichnis verwendet. 
    
    Mit dem obigen Skript zum Vorbereiten des Geräts werden die Aufgaben automatisiert, damit Sie nur einen Befehl ausführen müssen, um nahtlos alle relevanten Eingabe- und Konfigurationsordner, Videoeingabedateien und Benutzerkonten mit Berechtigungen zu erstellen. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollten auf Ihrem Edgegerät die folgenden Ordner erstellt worden sein. 
    
      * `/home/localedgeuser/samples`
      * `/home/localedgeuser/samples/input`
      * `/var/lib/videoanalyzer`
      * `/var/media`
    
     Achten Sie auf die Videodateien (*.mkv) im Ordner „/home/localedgeuser/samples/input“, die als Eingabedateien für die Analyse dienen.  

1. Stellen Sie als Nächstes die anderen Azure-Ressourcen bereit.

   [![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

   > [!NOTE]
   > Über die obige Schaltfläche wird der virtuelle Standardcomputer erstellt und verwendet. Dieser verfügt NICHT über die NVIDIA-GPU. Verwenden Sie die Option „Vorhandenes Edgegerät verwenden“, wenn Sie in der ARM-Vorlage (Azure Resource Manager) danach gefragt werden, und verwenden Sie die IoT Hub-Instanz und die Geräteinformationen aus dem obigen Schritt.
   > :::image type="content" source="./media/spatial-analysis/use-existing-device.png" alt-text="Verwenden eines vorhandenen Geräts":::

   [!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Übersicht

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/overview.png" alt-text="Übersicht über die räumliche Analyse":::

In diesem Diagramm ist der Fluss der Signale in diesem Tutorial dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](../pipeline.md#rtsp-source) ruft den Videofeed von diesem Server ab und sendet Videoframes an den Knoten des `CognitiveServicesVisionProcessor`.

Der Knoten `CognitiveServicesVisionProcessor` fungiert als Proxy. Er wandelt die Video-Einzelbilder in den angegebenen Bildtyp um. Anschließend leitet er das Bild über **gemeinsam genutzten Speicher** an ein anderes Edge-Modul weiter, das KI-Vorgänge hinter einem gRPC-Endpunkt ausführt. In diesem Beispiel ist das Edge-Modul das Modul „spatial-analysis“. Der Knoten `CognitiveServicesVisionProcessor` hat zwei Aufgaben:

- Er sammelt die Ergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Senke](../pipeline.md#iot-hub-message-sink). Der Knoten sendet diese Ereignisse dann an den [IoT Edge Hub-](../../../iot-fundamentals/iot-glossary.md#iot-edge-hub).
- Er erfasst außerdem mit einem [Signalgateprozessor](../pipeline.md#signal-gate-processor) einen Videoclip von 30 Sekunden aus der RTSP-Quelle und speichert ihn als Videosenke.

## <a name="create-the-computer-vision-resource"></a>Erstellen der Ressource für maschinelles Sehen

Sie müssen eine Azure-Ressource vom Typ [Maschinelles Sehen](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) für die **S1-Standardschicht** erstellen, entweder im [Azure-Portal](../../../iot-edge/how-to-deploy-modules-portal.md) oder über die Azure-Befehlszeilenschnittstelle. 

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

[!INCLUDE [setup development environment](./includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]

## <a name="configure-deployment-template"></a>Konfigurieren der Bereitstellungsvorlage
#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)
Suchen Sie in **/src/edge/deployment.spatialAnalysis.ase.template.json** nach der Bereitstellungsdatei. In der Vorlage sind das `avaedge`-Modul, das `rtspsim`-Modul und unser `spatialanalysis`-Modul enthalten. 

In Ihrer Bereitstellungsvorlagendatei:

1. Das Bereitstellungsmanifest verwendet Port 50051 für die Kommunikation zwischen `avaedge` und dem `spatialanalysis`-Modul. Wenn der Port von einer anderen Anwendung verwendet wird, legen Sie die Portbindung im Modul `spatialanalysis` auf einen geöffneten Port fest.

   ```
   "PortBindings": {
       "50051/tcp": [
           {
               "HostPort": "50051"
           }
       ]
   }
   ```

1. Die Erstellungsoptionen (createOptions) von `IpcMode` in den Modulen `avaedge` und `spatialanalysis` müssen identisch und auf **host** festgelegt sein.
1. Damit der RTSP-Simulator funktioniert, müssen bei Verwendung eines Azure Stack Edge-Geräts Volumegrenzen eingerichtet worden sein.

   1. [Stellen Sie eine Verbindung mit der SMB-Freigabe her](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share), und kopieren Sie die [Einzelhandels-Beispielvideodatei](https://avamedia.blob.core.windows.net/public/retailshop-15fps.mkv) in die lokale Freigabe.

      > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWMIPP]

   1. Das Modul „rtspsim“ muss folgende Konfiguration aufweisen:
      ```
      "createOptions": {
                          "HostConfig": {
                            "Mounts": [
                              {
                                "Target": "/live/mediaServer/media",
                                "Source": "<your Local Docker Volume Mount name>",
                                "Type": "volume"
                              }
                            ],
                            "PortBindings": {
                              "554/tcp": [
                                {
                                  "HostPort": "554"
                                }
                              ]
                            }
                          }
                        }
      ```
#### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)
Die Bereitstellungsdatei finden Sie unter **/src/edge/deployment.spatialAnalysis.template.json**. In der Vorlage sind das `avaedge`-Modul, das `rtspsim`-Modul und unser `spatialanalysis`-Modul enthalten. 

#### <a name="azure-vm-with-gpu"></a>[Azure-VM mit GPU](#tab/virtual-machine)
Die Bereitstellungsdatei finden Sie unter **/src/edge/deployment.spatialAnalysis.template.json**. In der Vorlage sind das `avaedge`-Modul, das `rtspsim`-Modul und unser `spatialanalysis`-Modul enthalten.

---

In der folgenden Tabelle sind die verschiedenen Umgebungsvariablen aufgeführt, die vom IoT Edge-Modul verwendet werden. Sie können sie auch im oben genannten Bereitstellungsmanifest festlegen, indem Sie das Attribut `env` in `spatialanalysis` verwenden:

| Einstellungsname | Wert | BESCHREIBUNG|
|---------|---------|---------|
| DISPLAY | :1 | Dieser Wert muss der Ausgabe von `echo $DISPLAY` auf dem Hostcomputer entsprechen. Azure Stack Edge-Geräte haben kein Display. Diese Einstellung ist nicht anwendbar.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | **Nicht ändern**|
| ARCHON_LOG_LEVEL | Info, Verbose (Information, Ausführlich) | Protokolliergrad: Wählen Sie einen der beiden Werte aus.|
| QT_X11_NO_MITSHM | 1 | **Nicht ändern**|
| OMP_WAIT_POLICY | PASSIVE | **Nicht ändern**|
| ENDBENUTZER-LIZENZVERTRAG | accept (Akzeptieren) | Sie müssen diesen Wert auf *accept* (Akzeptieren) festlegen, damit der Container ausgeführt werden kann. |
| ARCHON_TELEMETRY_IOTHUB | true | Legen Sie diesen Wert auf TRUE fest, um die Telemetrieereignisse an IoT Hub zu senden |
| ABRECHNUNG | Ihr Endpunkt-URI| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn auf dem Blatt **Schlüssel und Endpunkt** für Ihre Ressource.|
| APIKEY | Ihr API-Schlüssel| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn auf dem Blatt **Schlüssel und Endpunkt** für Ihre Ressource. |
| LAUNCHER_TYPE | avaBackend | **Nicht ändern** |
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Fügen Sie diese Umgebungsvariable hinzu, wenn Ihre GPU **nicht** T4 oder NVIDIA 2080 Ti ist.|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet. 

### <a name="gathering-keys-and-endpoint-uri"></a>Sammeln von Schlüsseln und Endpunkt-URI

Ein API-Schlüssel wird zum Starten des Containers „spatial-analysis“ verwendet. Er ist im Azure-Portal auf der Seite `Keys and Endpoint` Ihrer Ressource für maschinelles Sehen verfügbar. Navigieren Sie zu dieser Seite, und suchen Sie den Schlüssel und den Endpunkt-URI, die vom `spatialAnalysis`-Container benötigt werden.  

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis/keys-endpoint.png" alt-text="Endpunkt-URI":::

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generieren und Bereitstellen des Bereitstellungsmanifests

Mit dem Bereitstellungsmanifest wird definiert, welche Module auf einem Edgegerät bereitgestellt werden. Darüber hinaus werden auch die Konfigurationseinstellungen für diese Module definiert.

Führen Sie die folgenden Schritte aus, um das Manifest auf der Grundlage der Vorlagendatei zu generieren und dann auf dem Edgegerät bereitzustellen.

1. Öffnen Sie Visual Studio Code.
1. Wählen Sie neben dem Bereich `AZURE IOT HUB` das Symbol „Weitere Aktionen“ aus, um die IoT Hub-Verbindungszeichenfolge festzulegen. Sie können die Zeichenfolge aus der Datei `src/cloud-to-device-console-app/appsettings.json` kopieren.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/set-connection-string.png" alt-text="Räumliche Analyse: Verbindungszeichenfolge":::

1. Klicken Sie im Ordner-Explorer mit der rechten Maustaste auf die Bereitstellungsvorlagendatei, und wählen Sie „IoT Edge Bereitstellungsmanifest generieren“ aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/generate-deployment-manifest.png" alt-text="Räumliche Analyse: Datei „deployment.amd64.json“":::

   Dadurch sollte im Ordner **src/edge/config** eine Manifestdatei erstellt werden.

1. Klicken Sie mit der rechten Maustaste auf die generierte Manifestdatei, wählen Sie **Create Deployment for Single Device** (Bereitstellung für einzelnes Gerät erstellen) und anschließend den Namen Ihres Edgegeräts aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/deployment-single-device.png" alt-text="Räumliche Analyse: Bereitstellen für einzelnes Gerät":::

1. Am oberen Seitenrand werden Sie aufgefordert, ein IoT Hub-Gerät auszuwählen. Wählen Sie im Dropdownmenü den Namen Ihres Edgegeräts aus.
1. Aktualisieren Sie nach ungefähr 30 Sekunden links unten im Fenster den Bereich **AZURE IOT HUB**. Das Edge-Gerät zeigt nun die folgenden bereitgestellten Module an:

   - Azure Video Analyzer (Modulname **avaedge**)
   - RTSP-Simulator (Real-Time Streaming Protocol) (Modulname **rtspsim**)
   - Räumliche Analyse (Modulname **spatialanalysis**)

Nach erfolgreicher Bereitstellung wird im Ausgabefenster eine Meldung wie die folgende angezeigt:

```
[Edge] Start deployment to device [<edge device name>]
[Edge] Deployment succeeded.
```

Anschließend finden Sie unter „Geräte/Module“ die Module `avaedge`, `spatialanalysis` und `rtspsim`, und ihr Status sollte **Wird ausgeführt** lauten.

## <a name="prepare-to-monitor-events"></a>Vorbereiten der Überwachung von Ereignissen

Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach Azure IoT Hub.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/extension-settings.png" alt-text="Erweiterungseinstellungen":::

1. Suchen Sie nach dem Kontrollkästchen „Show Verbose Message“ (Ausführliche Meldung anzeigen), und aktivieren Sie es.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::

1. Öffnen Sie den Explorer-Bereich, und suchen Sie in der linken unteren Ecke nach **AZURE IOT HUB**. Klicken Sie mit der rechten Maustaste, und wählen Sie „Überwachung des integrierten Ereignisendpunkts starten“ aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/vscode-common-screenshots/start-monitoring.png" alt-text="Räumliche Analyse: Starten der Überwachung":::

## <a name="run-the-program"></a>Ausführen des Programms

Durch „program.cs“ werden die direkten Methoden in `src/cloud-to-device-console-app/operations.json` aufgerufen. Sie müssen die `operations.json`-Datei bearbeiten und die Pipelinetopologie-URL, den Namen der Topologie sowie die RTSP-URL aktualisieren.

In „operations.json“:

- Legen Sie die Pipelinetopologie wie folgt fest:

  ```json
  {
      "opName": "pipelineTopologySet",
      "opParams": {
          "pipelineTopologyUrl": "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-zone-crossing-operation-topology.json"
      }
  },
  ```
* Bearbeiten Sie unter `livePipelineSet` den Namen der Topologie, damit er mit dem Wert im vorherigen Link übereinstimmt:
    * `"topologyName" : "PersonZoneCrossingTopology"`
* Ändern Sie unter `pipelineTopologyDelete` den Namen:
    * `"name" : "PersonZoneCrossingTopology"`

> [!Important]
> Die oben verwendete Topologie hat einen hart codierten Namen für die VideoSink-Ressource `videoSink`. Wenn Sie sich für eine andere Videoquelle entscheiden, denken Sie daran, diesen Wert zu ändern.

- Erstellen Sie wie folgt eine Livepipeline, und legen Sie dort die Parameter für die Pipelinetopologie fest:

  ```json
  {
      "opName": "livePipelineSet",
      "opParams": {
          "name": "Sample-Pipeline-1",
          "properties": {
              "topologyName": "PersonZoneCrossingTopology",
              "description": "Sample pipeline description",
              "parameters": [
                  {
                      "name": "rtspUrl",
                      "value": "rtsp://rtspsim:554/media/retailshop-15fps.mkv"
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
  },
  ```

Drücken Sie F5, um eine Debugsitzung zu starten, und befolgen Sie die Anweisungen im **TERMINAL**, um die Pipelinetopologie festzulegen, die Livepipeline festzulegen, die Livepipeline zu aktivieren und schließlich die Ressourcen zu löschen.

> [!Note]
> Das Programm wird beim Schritt „Livepipeline aktivieren“ angehalten. Öffnen Sie die Registerkarte „Terminal“, und drücken Sie die **EINGABETASTE**, um fortzufahren. Starten Sie dann die Schritte zum Deaktivieren und Löschen von Ressourcen.

## <a name="interpret-results"></a>Interpretieren von Ergebnissen

`spatialanalysis` ist ein großer Container, dessen Startzeit bis zu 30 Sekunden betragen kann. Sobald der Container „spatialanalysis“ ausgeführt wird und verfügbar ist, werden die Rückschlussereignisse gesendet. Es werden Ereignisse in dieser Art angezeigt:

```JSON
[IoTHubMonitor] [3:37:28 PM] Message received from [ase03-edge/avaedge]:
{
  "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620671848135494 1 IN IP4 172.27.86.122\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/cafeteria.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.066\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/retailshop-15fps.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCHoQAAAMABAAAAwDwPGDGWA==,aOvssiw=\r\na=control:track1\r\n"
}
[IoTHubMonitor] [3:37:30 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}
[IoTHubMonitor] [3:37:40 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:37:50 PM] Message received from [ase03-edge/avaedge]:
{
  "state&quot;: &quot;initializing"
}
[IoTHubMonitor] [3:38:18 PM] Message received from [ase03-edge/avaedge]:
{
  "type": "video",
  "location": "/videos/<your video name>",
  "startTime&quot;: &quot;2021-05-10T18:37:27.931Z"
}

```
> [!NOTE]
> Die **„initializing“** -Meldungen werden angezeigt. Diese Meldungen erscheinen während des Starts des Moduls „spatialAnalysis“, und es kann bis zu 60 Sekunden dauern, bis der Zustand „Wird ausgeführt“ erreicht ist. Wenn Sie ein wenig Geduld haben, sollte der Durchlauf des Rückschlussereignisses zu sehen sein.

Wenn ein Pipelinetopologie instanziiert wird, sollte das Ereignis „MediaSessionEstablished“ angezeigt werden. Ein [Beispiel für das Ereignis MediaSessionEstablished](detect-motion-emit-events-quickstart.md#mediasessionestablished-event) finden Sie hier.

Das Modul „spatialanalysis“ sendet auch KI Insights-Ereignisse an Azure Video Analyzer und anschließend an IoT Hub. Dies wird auch im Ausgabefenster angezeigt. Diese KI-Erkenntnisse werden zusammen mit Videos über den Knoten der Videosenke aufgezeichnet. Sie können Video Analyzer verwenden, um sie anzuzeigen, wie unten beschrieben.

## <a name="supported-spatial-analysis-operations"></a>Unterstützte Vorgänge zur räumlichen Analyse

Dies sind die Vorgänge, die das `spatialAnalysis`-Modul anbietet und die von Azure Video Analyzer unterstützt werden:

- **personZoneCrossing**
- **personCrossingLine**
- **personDistance**
- **personCount**
- **customOperation**


Lesen Sie unser Referenzdokument zu **unterstützten** **[Räumlichen Analysevorgängen](../spatial-analysis-operations.md)** , um mehr über die verschiedenen Vorgänge und die in ihnen unterstützten Eigenschaften zu erfahren.

## <a name="playing-back-the-recording"></a>Wiedergeben der Aufzeichnung

Sie können die von der Livepipeline erstellte Video Analyzer-Videoressource untersuchen, indem Sie sich beim Azure-Portal anmelden und das Video anzeigen.

1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie unter den Ressourcen Ihres Abonnements nach Ihrem Video Analyzer-Konto, und öffnen Sie den Kontobereich.
1. Wählen Sie in der Liste **Video Analyzers** die Option **Videos** aus.
1. Ein Video mit dem Namen `personzonecrossing` ist aufgeführt. Dies ist der Name, den Sie in Ihrer Datei mit der Pipelinetopologie ausgewählt haben.
1. Wählen Sie das Video aus.
1. Klicken Sie auf der Seite mit den Videodetails auf das **Wiedergabesymbol**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback.png" alt-text="Screenshot: Videowiedergabe":::
   
1. Um die Rückschlussmetadaten im Video anzuzeigen, klicken Sie auf das Symbol zum **Rendern von Metadaten**.
   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/record-stream-inference-data-with-video/bounding-box.png" alt-text="Symbol für das Rendern von Metadaten":::

    Sie finden drei Optionen, die Sie als Überlagerung im Video anzeigen können:  
      - **Begrenzungsfelder**: Zeigt um jede Person umrandende Begrenzungsfelder mit einer eindeutigen ID an.
      - **Attribute**: Anzeigen von Personenattributen wie Geschwindigkeit (in Fuß/s) und Ausrichtung (mithilfe eines Pfeils), falls verfügbar
      - **Objektpfad**: Zeigt einen kurzen Pfad für die Bewegung jeder Person an, falls verfügbar.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/spatial-analysis/sa-video-playback-bounding-boxes.png" alt-text="Screenshot der Videowiedergabe mit Begrenzungsfeldern":::

[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie verschiedene Vorgänge des `spatialAnalysis`-Moduls aus. Weitere Informationen finden Sie unter den folgenden Pipelinetopologien:

- [personCount](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-count-operation-topology.json)
- [personDistance](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-distance-operation-topology.json)
- [personCrossingLine](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/person-line-crossing-operation-topology.json)
- [customOperation](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/spatial-analysis/custom-operation-topology.json)
