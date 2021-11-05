---
title: Verbinden einer Kamera mit der Cloud mithilfe eines Remotegeräteadapters
description: In diesem Artikel wird erläutert, wie Sie eine Kamera mithilfe eines Remotegeräteadapters mit dem Azure Video Analyzer-Dienst verbinden.
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 71bcf82420d9777158cbb878c4943b350d51353b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095051"
---
# <a name="connect-cameras-to-the-cloud-using-a-remote-device-adapter"></a>Verbinden von Kameras mit der Cloud mithilfe eines Remotegeräteadapters

[!INCLUDE [header](includes/cloud-env.md)]

Mit dem Azure Video Analyzer-Dienst können Benutzer Videodaten von RTSP-Kameras erfassen und aufzeichnen, die mit der Cloud verbunden sind. Dies erfordert, dass auf diese Kameras über das Internet zugegriffen werden kann, was in vielen Fällen unzulässig sein kann. Stattdessen können Sie das Video Analyzer-Edgemodul auf einem schlanken Edgegerät im gleichen (privaten) Netzwerk wie die RTSP-Kameras bereitstellen und das Edgegerät mit dem Internet verbinden. Das Edgemodul kann jetzt als *Adapter* eingerichtet werden, mit dem der Video Analyzer-Dienst eine Verbindung mit den *Remotegeräten* (Kameras) herstellen kann. Das Edgemodul fungiert als [transparentes Gateway ](../../../iot-edge/iot-edge-as-gateway.md) für Videodatenverkehr zwischen den RTSP-Kameras und dem Video Analyzer-Dienst.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-remote-device-adapter/use-remote-device-adapter.svg" alt-text="Verbinden von Kameras mit der Cloud mithilfe eines Remotegeräteadapters":::

## <a name="pre-reading"></a>Vorbereitung

* [Verbinden von Kameras mit der Cloud](connect-cameras-to-cloud.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.
* IoT Hub
* [Video Analyzer-Konto](../create-video-analyzer-account.md) mit diesen zugeordneten Elementen:
  * Speicherkonto
  * vom Benutzer zugewiesene verwaltete Identität (User-assigned Managed Identity, UAMI)
  * [IoT Hub muss mit dem Video Analyzer-Konto verbunden sein](../create-video-analyzer-account.md#post-deployment-steps)
* [IoT-Edgegerät mit installiertem und konfiguriertem Video Analyzer-Edgemodul](../edge/deploy-iot-edge-device.md)
* [RTSP-Kameras](../quotas-limitations.md#supported-cameras)
  * Vergewissern Sie sich, dass sich die Kameras im gleichen Netzwerk wie das Edgegerät befinden.
  * Vergewissern Sie sich, dass Sie die Kamera so konfigurieren können, dass Videos mit oder unter einer maximalen Bandbreite (gemessen in KBit/s oder Kilobit/Sekunde) gesendet werden.

## <a name="overview"></a>Übersicht
Sie müssen Folgendes ausführen, um eine Kamera mithilfe eines Remotegeräteadapters mit dem Video Analyzer-Dienst zu verbinden:

1. Erstellen eines IoT-Geräts in IoT Hub zur Darstellung der RTSP-Kamera
1. Erstellen eines Geräteadapters auf dem Video Analyzer-Edgemodul, das als transparentes Gateway für das oben genannte Gerät dient
1. Verwenden Sie das IoT-Gerät und den Geräteadapter, wenn Sie eine Livepipeline im Video Analyzer-Dienst erstellen, um Videodaten von der Kamera zu erfassen und aufzuzeichnen.



## <a name="create-an-iot-device"></a>Erstellen eines IoT-Geräts

Erstellen Sie ein IoT-Gerät, um jede RTSP-Kamera darzustellen, die mit dem Video Analyzer-Dienst verbunden werden muss. Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zum IoT Hub
1. Wählen Sie unter **Geräteverwaltung** den Bereich **Geräte** aus.
1. Wählen Sie die Schaltfläche **+ Gerät hinzufügen** aus. 
1. Geben Sie eine **Geräte-ID** mithilfe einer eindeutigen Zeichenfolge ein (z. B. building404-camera1).
1. Der **Authentifizierungstyp** muss **symmetrischer Schlüssel** lauten
1. Alle anderen Eigenschaften können auf dem Standardwert belassen werden
1. Wählen Sie **Speichern** aus, um das IoT-Gerät zu erstellen.
1. Wählen Sie das IoT-Gerät aus, und notieren Sie sich den **Primär-** oder **Sekundärschlüssel**, da er im weiteren Verlauf benötigt wird.

## <a name="create-a-remote-device-adapter"></a>Erstellen eines Remotegeräteadapters

Damit das Video Analyzer-Edgemodul als transparentes Gateway für Videos zwischen der Kamera und dem Video Analyzer-Dienst fungieren kann, müssen Sie für jede Kamera einen Remotegeräteadapter erstellen. Rufen Sie die [direkte Methode **remoteDeviceAdapterSet**](../edge/direct-methods.md) auf, für die folgende Werte erforderlich sind: 

* Geräte-ID für das IoT-Gerät
* Primärschlüssel für das IoT-Gerät
* IP-Adresse der Kamera

Führen Sie im Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie zum IoT Hub
1. Wählen Sie unter **Automatische Geräteverwaltung** den Bereich **IoT Edge** aus.
1. Wählen Sie das IoT-Edgegerät (wie etwa **ava-sample-device**) aus, auf dem das Video Analyzer-Edgemodul bereitgestellt wurde
1. Wählen Sie unter dem Modulen das Video Analyzer-Edgemodul aus (z. B. **avaedge**)
1. Wählen Sie **</> Direkte Methode** aus 
1. Geben Sie als Methodennamen **remoteDeviceAdapterSet** ein.
1. Geben Sie für **Payload** (Nutzlast) Folgendes ein:

```
 {
   "@apiVersion" : "1.1",
   "name": "<name of remote device adapter such as remoteDeviceAdapterCamera1>",
   "properties": {
     "target": {
       "host": "<Camera's IP address>"
      },
     "iotHubDeviceConnection": {
      "deviceId": "<IoT Hub Device ID>",
      "credentials": {
        "@type": "#Microsoft.VideoAnalyzer.SymmetricKeyCredentials",
        "key": "<Primary or Secondary Key>"
       }
     }
   }
 }
 
```

Bei Erfolg erhalten Sie eine Antwort mit dem Statuscode 201.


## <a name="create-pipeline-topology-in-the-video-analyzer-service"></a>Erstellen einer Pipelinetopologie im Video Analyzer-Dienst

Beim Erstellen einer Cloudpipelinetopologie für die Erfassung von einer Kamera hinter einer Firewall muss auf dem RTSP-Quellknoten der Pipelinetopologie Tunneln aktiviert werden. Sehen Sie sich ein Beispiel für eine solche [Pipelinetopologie](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cloud-record-camera-behind-firewall) an.  

[In dieser Schnellstartanleitung](get-started-livepipelines-portal.md#deploy-a-live-pipeline) werden die Schritte zum Erstellen einer Pipelinetopologie und einer Livepipeline im Azure-Portal beschrieben. Verwenden Sie die Beispieltopologie `Live capture, record, and stream from RTSP camera behind firewall`.

Die folgenden Werte, die auf dem in den vorherigen Anweisungen bereitgestellten IoT-Gerät basieren, sind erforderlich, um das Tunneln auf dem RTSP-Quellknoten zu aktivieren:

* IoT Hub-Name
* IoT Hub-Geräte-ID

```
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "url": "${rtspUrlParameter}",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUsernameParameter}",
                        "password": "${rtspPasswordParameter}"
                    },
                    "tunnel": { 
                        "@type": "#Microsoft.VideoAnalyzer.SecureIotDeviceRemoteTunnel",
                        "iotHubName" : "<IoT Hub Name>",
                        "deviceId": "${ioTHubDeviceIdParameter}"
                    }
                }
            }
``` 

Stellen Sie Folgendes sicher:
* `Transport` auf `tcp`
* `Endpoint` auf `UnsecuredEndpoint`
* `Tunnel` auf `SecureIotDeviceRemoteTunnel`

## <a name="create-and-activate-a-live-pipeline"></a>Erstellen und Aktivieren einer Livepipeline

Beim Erstellen der Livepipeline müssen die RTSP-URL, der RTSP-Benutzername, das RTSP-Kennwort und die IoT Hub-Geräte-ID definiert werden. Eine Beispielnutzlast finden Sie unten.

```
   {
    "name": "record-from-building404-camera1",
    "properties": {
        "topologyName": "record-camera-behind-firewall",
        "description": "Capture, record and stream video from building404-camera1 via a remote device adapter",
        "bitrateKbps": 1500,
        "parameters": [
            {
                "name": "rtspUrlParameter",
                "value": "rtsp://localhost:554/<camera-specific-suffix>"
            },
            {
                "name": "rtspUsernameParameter",
                "value": "<User name for building404-camera1>"
            },
            {
                "name": "rtspPasswordParameter",
                "value": "<Password for building404-camera1>"
            },
            {
                "name": "ioTHubDeviceIdParameter",
                "value": "<IoT Hub Device ID such as building404-camera1>"
            },
            {
                "name": "videoName",
                "value": "video-from-building404-camera1"
            }
          ]
       }
   }
```
Die RTSP-URL muss **localhost** lauten. Vergewissern Sie sich, dass der Wert `bitrateKbps` mit der Einstellung für die maximale Bitrate der Videodaten von der RTSP-Kamera übereinstimmt.

Nach dem Erstellen der Livepipeline kann die Pipeline aktiviert werden, um die Aufzeichnung in die Video Analyzer-Videoressource zu starten. In der im vorherigen Schritt erwähnten [Schnellstartanleitung](get-started-livepipelines-portal.md#deploy-a-live-pipeline) wird auch beschrieben, wie Sie eine Livepipeline in Azure-Portal aktivieren.


### <a name="playback-recorded-video-in-the-azure-portal"></a>Wiedergeben von aufgezeichneten Videodaten im Azure-Portal

1. Nach dem Aktivieren der Livepipeline ist die Videoressource im Bereich **Videos** des Video Analyzer-Kontos im Azure-Portal verfügbar. Der Status lautet **In Verwendung**, da die Pipeline aktiv ist und aufzeichnet.
1. Wählen Sie die Videoressource aus, die in der Livepipeline definiert wurde, um das Video anzuzeigen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Screenshot des Livevideos, das von der Livepipeline in der Cloud erfasst wurde.":::

Wenn beim Wiedergeben des Videos Fehler auftreten, führen Sie die Schritte in [diesem Leitfaden zur Problembehandlung](troubleshoot.md#unable-to-play-video-after-activating-live-pipeline) aus.

[!INCLUDE [activate-deactivate-pipeline](../edge/includes/common-includes/activate-deactivate-pipeline.md)]

## <a name="next-steps"></a>Nächste Schritte

Da nun ein Video in Ihrem Video Analyzer-Konto vorhanden ist, können Sie [mithilfe dieses Tutorials](export-portion-of-video-as-mp4.md) einen Clip dieses aufgezeichneten Videos in das MP4-Format exportieren.
