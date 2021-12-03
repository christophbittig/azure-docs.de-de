---
title: Erkennen von Bewegung, Aufzeichnen von Video in Azure Video Analyzer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie das Edgemodul von Azure Video Analyzer verwenden, um Bewegungen in einem Livevideostream zu erkennen und Videos im Video Analyzer-Konto aufzuzeichnen.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 48c41b812a4cdbd416d0dd146aba938cce419864
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484203"
---
# <a name="quickstart-detect-motion-record-video-to-video-analyzer"></a>Schnellstart: Erkennen von Bewegung, Aufzeichnen von Video in Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

In diesem Artikel werden die Schritte zur Verwendung des Azure Video Analyzer-Edgemoduls für die [ereignisbasierte Aufzeichnung](../event-based-video-recording-concept.md) beschrieben. Dabei werden ein virtueller Linux-Computer in Azure als IoT Edge-Gerät und ein simulierter Livevideostream verwendet. Der Videostream wird auf das Vorhandensein sich bewegender Objekte analysiert. Wenn Bewegungen erkannt werden, werden Ereignisse an Azure IoT Hub gesendet, und der relevante Teil des Videostreams wird als [Videoressource](../terminology.md#video) in Ihrem Video Analyzer-Konto aufgezeichnet.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto das ein aktives Abonnement beinhaltet. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keines besitzen.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

### <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

Der Bereitstellungsprozess dauert ungefähr **20 Minuten**. Nach Abschluss haben Sie bestimmte Azure-Ressourcen im Azure-Abonnement bereitgestellt. Hierzu zählen unter anderem:
1. **Video Analyzer-Konto:** Dieser [Clouddienst](../overview.md) wird zum Registrieren des Video Analyzer-Edgemoduls und zur Wiedergabe aufgezeichneter Videos und Videoanalysen verwendet.
1. **Speicherkonto:** Zum Speichern aufgezeichneter Videos und Videoanalysen.
1. **Verwaltete Identität** - Dies ist der Benutzer, dem [managed identity]../../../active-directory/managed-identities-azure-resources/overview.md) zugewiesen wurde, um den Zugriff auf das oben genannte Speicherkonto zu verwalten.
1. **Virtueller Computer:** Ein virtueller Computer, der als Ihr simuliertes Edgegerät dient.
1. **IoT Hub**: Dient als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung, IoT Edge-Modulen und den verwalteten Geräten.

Weitere Details finden Sie [hier](https://github.com/Azure/video-analyzer/tree/main/setup).

## <a name="review-the-sample-video"></a>Überprüfen des Beispielvideos

Auf dem virtuellen Computer, der bei der obigen Bereitstellung erstellt wurde, befinden sich mehrere MKV-Dateien.  Eine dieser Dateien hat den Namen `lots_015.mkv`. In den folgenden Schritten verwenden wir diese Videodatei, um einen Livestream für dieses Tutorial zu simulieren.

Sie können eine Anwendung wie [VLC Player](https://www.videolan.org/vlc/) starten, `Ctrl+N` drücken und diesen Link zum [Parkplatz-Videobeispiel](https://avamedia.blob.core.windows.net/public/lots_015.mkv) einfügen, um mit der Wiedergabe zu beginnen. Etwa bei der 5-Sekunden-Marke fährt ein weißes Fahrzeug über den Parkplatz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LUbN]

Nach Abschluss der nachfolgenden Schritte haben Sie mithilfe den Video Analyzer-Edgemoduls die Bewegung des Fahrzeugs erkannt und einen Videoclip aufgezeichnet, der ungefähr bei dieser 5-Sekunden-Marke beginnt. 

In der Abbildung unten ist der gesamte Ablauf visuell dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/detect-motion-record-video-clips-cloud/topology.svg" alt-text="Ereignisbasierte Videoaufzeichnung einer Videoressource basierend auf Bewegungsereignissen":::

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

### <a name="obtain-your-iot-hub-connection-string"></a>Abrufen der IoT Hub-Verbindungszeichenfolge

1. Navigieren Sie im Azure-Portal zu dem IoT Hub, den Sie im obigen Einrichtungsschritt erstellt haben.
1. Suchen Sie im linken Navigationsbereich nach der Option **Freigegebene Zugriffsrichtlinien**, und klicken Sie darauf.
1. Klicken Sie auf die Richtlinie mit dem Namen **iothubowner**.
1. Kopieren Sie die **primäre Verbindungszeichenfolge**. Diese sieht wie `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX` aus.

### <a name="connect-to-the-iot-hub"></a>Herstellen der Verbindung mit dem IoT Hub

1. Öffnen Sie Visual Studio Code, und wählen Sie die Option **Ansicht** > **Explorer** aus. Oder drücken Sie STRG+UMSCHALT+E.
1. Wählen Sie unten links auf der Registerkarte **Explorer** die Option **Azure IoT Hub** aus.
1. Wählen Sie das Symbol **Weitere Optionen** aus, um das Kontextmenü anzuzeigen. Wählen Sie anschließend **IoT Hub-Verbindungszeichenfolge festlegen** aus.
1. Geben Sie Ihre IoT Hub-Verbindungszeichenfolge ein, wenn das entsprechende Eingabefeld angezeigt wird.
1. Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich. Es sollte das Edgegerät `avasample-iot-edge-device` angezeigt werden, auf dem die folgenden Module bereitgestellt sind:
    * Video Analyzer-Edgemodul (Modulname **avaedge**)
    * RTSP-Simulator (Modulname **rtspsim**)


> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Erweitern des Knotens „Module“":::

> [!TIP]
> Wenn Sie [Video Analyzer manuell auf einem Edgegerät (z. B. einem ARM64-Gerät) bereitgestellt haben](deploy-iot-edge-device.md), wird das Modul unter dem Azure IoT Hub unter diesem Gerät angezeigt. Sie können dieses Modul auswählen. Führen Sie dann die folgenden restlichen Schritte aus.

### <a name="prepare-to-monitor-the-modules"></a>Vorbereiten der Überwachung der Module 

Wenn Sie diese Schnellstartanleitung verwenden, werden Ereignisse an den IoT Hub gesendet. Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach **Azure IoT Hub**.
1. Klicken Sie mit der rechten Maustaste, und wählen Sie **Erweiterungseinstellungen** aus.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Auswählen von „Erweiterungseinstellungen“":::
1. Suchen Sie nach „Ausführliche Meldung anzeigen“, und aktivieren Sie die Option.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Show Verbose Message (Ausführliche Meldung anzeigen)":::
1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, und suchen Sie links unten nach **Azure IoT Hub**.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf `avasample-iot-edge-device`, und wählen Sie die Option **Überwachung des integrierten Ereignisendpunkts starten** aus.

    [!INCLUDE [provide-builtin-endpoint](./includes/common-includes/provide-builtin-endpoint.md)]

## <a name="use-direct-method-calls-to-analyze-live-video"></a>Verwenden direkter Methodenaufrufe zum Analysieren von Livevideos

Sie können jetzt Livevideostreams analysieren, indem Sie direkte Methoden aufrufen, die vom Video Analyzer-Edgemodul verfügbar gemacht werden. Lesen Sie [Direkte Methoden in Video Analyzer](direct-methods.md), um alle direkten Methoden zu untersuchen, die vom Modul bereitgestellt werden.

### <a name="enumerate-pipeline-topologies"></a>Aufzählen von Pipelinetopologien

In diesem Schritt werden alle [Pipelinetopologien](../pipeline.md) im Modul aufgelistet.

1. Klicken Sie mit der rechten Maustaste auf das Modul „avaedge“, und wählen Sie im Kontextmenü die Option **Invoke Module Direct Method** (Direkte Methode des Moduls aufrufen) aus.
1. Oben in der Mitte des Visual Studio Code-Fensters wird ein Bearbeitungsfeld angezeigt. Geben Sie „pipelineTopologyList“ in das Bearbeitungsfeld ein, und drücken Sie die EINGABETASTE.
1. Kopieren Sie die folgende JSON-Nutzlast, fügen Sie sie im Bearbeitungsfeld ein, und drücken Sie die EINGABETASTE.
   
```json
{
    "@apiVersion" : "1.1"
}
```

Innerhalb weniger Sekunden wird die folgende Antwort im Ausgabefenster angezeigt:
    
```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

Die Antwort oben entspricht den Erwartungen, da keine Pipelinetopologien erstellt wurden.

### <a name="set-a-pipeline-topology"></a>Festlegen einer Pipelinetopologie

Anhand der gleichen Schritte wie oben können Sie `pipelineTopologySet` aufrufen, um eine Pipelinetopologie mit dem folgenden JSON-Code als Nutzlast festzulegen: Sie erstellen eine Pipelinetopologie mit dem Namen „EVRtoVideoSinkOnMotionDetection“.

> [!NOTE]
> In der Nutzlast unten ist die `videoName`-Eigenschaft auf „sample-motion-video-camera001“ festgelegt. Dies ist der Name der Videoressource, die in Ihrem Video Analyzer-Konto erstellt wird. Dieser Ressourcenname muss für jede Livevideoquelle, die Sie aufzeichnen, eindeutig sein. Sie sollten die `videoName`-Eigenschaft unten nach Bedarf so bearbeiten, dass sie eindeutig ist.

```
{
  "@apiVersion": "1.1",
  "name": "EVRtoVideoSinkOnMotionDetection",
  "properties": {
    "description": "Event-based video recording to Video Sink based on motion events",
    "parameters": [
      {
        "name": "rtspUserName",
        "type": "String",
        "description": "rtsp source user name.",
        "default&quot;: &quot;dummyUserName"
      },
      {
        "name": "rtspPassword",
        "type": "String",
        "description": "rtsp source password.",
        "default&quot;: &quot;dummyPassword"
      },
      {
        "name": "rtspUrl",
        "type": "String",
        "description&quot;: &quot;rtsp Url"
      },
      {
        "name": "motionSensitivity",
        "type": "String",
        "description": "motion detection sensitivity",
        "default&quot;: &quot;medium"
      },
      {
        "name": "hubSinkOutputName",
        "type": "String",
        "description": "hub sink output name",
        "default&quot;: &quot;inferenceOutput"
      }
    ],
    "sources": [
      {
        "@type": "#Microsoft.VideoAnalyzer.RtspSource",
        "name": "rtspSource",
        "endpoint": {
          "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
          "url": "${rtspUrl}",
          "credentials": {
            "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
            "username": "${rtspUserName}",
            "password&quot;: &quot;${rtspPassword}"
          }
        }
      }
    ],
    "processors": [
      {
        "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
        "name": "motionDetection",
        "sensitivity": "${motionSensitivity}",
        "inputs": [
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ]
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
        "name": "signalGateProcessor",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          },
          {
            "nodeName": "rtspSource",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "activationEvaluationWindow": "PT1S",
        "activationSignalOffset": "PT0S",
        "minimumActivationTime": "PT30S",
        "maximumActivationTime&quot;: &quot;PT30S"
      }
    ],
    "sinks": [
      {
        "@type": "#Microsoft.VideoAnalyzer.VideoSink",
        "name": "videoSink",
        "videoName": "sample-motion-video-camera001",
        "inputs": [
          {
            "nodeName": "signalGateProcessor",
            "outputSelectors": [
              {
                "property": "mediaType",
                "operator": "is",
                "value&quot;: &quot;video"
              }
            ]
          }
        ],
        "videoCreationProperties": {
          "title": "sample-motion-video-camera001",
          "description": "Motion-detection based recording of clips to a video resource",
          "segmentLength&quot;: &quot;PT30S"
        },
        "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
        "localMediaCacheMaximumSizeMiB&quot;: &quot;2048"
      },
      {
        "@type": "#Microsoft.VideoAnalyzer.IoTHubMessageSink",
        "name": "hubSink",
        "hubOutputName": "${hubSinkOutputName}",
        "inputs": [
          {
            "nodeName&quot;: &quot;motionDetection"
          }
        ]
      }
    ]
  }
}
```

Die oben verwendete JSON-Nutzlast bewirkt, dass eine Pipelinetopologie erstellt wird, mit der fünf Parameter (vier davon mit Standardwerten) definiert werden. Die Topologie umfasst einen Quellknoten ([RTSP-Quelle](../pipeline.md#rtsp-source)), zwei Prozessorknoten ([Bewegungserkennungsprozessor](../pipeline.md#motion-detection-processor) und [Signalgateprozessor](../pipeline.md#signal-gate-processor)) und zwei Senkenknoten (IoT Hub-Senke und [Videosenke](../pipeline.md#video-sink)). Die visuelle Darstellung der Topologie ist oben abgebildet.

Innerhalb weniger Sekunden wird die folgende Antwort im **Ausgabefenster** angezeigt.

```
[DirectMethod] Invoking Direct Method [pipelineTopologySet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```



Der zurückgegebene Status „201“ gibt an, dass eine neue Pipelinetopologie erstellt wurde. Probieren Sie als nächste Schritte die folgenden direkten Methoden aus:

* Rufen Sie wieder `pipelineTopologySet` auf, und überprüfen Sie, ob „200“ als Statuscode zurückgegeben wird. Der Statuscode „200“ gibt an, dass eine vorhandene Pipelinetopologie erfolgreich aktualisiert wurde.
* Rufen Sie `pipelineTopologySet` erneut auf, aber ändern Sie nun die Beschreibungszeichenfolge. Überprüfen Sie, ob in der Antwort der Statuscode „200“ zurückgegeben und die Beschreibung auf den neuen Wert aktualisiert wurde.
* Rufen Sie wie im vorherigen Schritt beschrieben `pipelineTopologyList` auf, und überprüfen Sie, ob nun die Topologie „EVRtoVideoSinkOnMotionDetection“ in der zurückgegebenen Nutzlast aufgeführt wird.

### <a name="read-the-pipeline-topology"></a>Lesen der Pipelinetopologie

Rufen Sie jetzt `pipelineTopologyGet` mit der folgenden Nutzlast auf:
```

{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden.

```
[DirectMethod] Invoking Direct Method [pipelineTopologyGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:17:53.483Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:17:53.483Z"
    },
    "name": "EVRtoVideoSinkOnMotionDetection",
    "properties": {
      "description": "Event-based video recording to Video Sink based on motion events",
      "parameters": [
        {
          "name": "hubSinkOutputName",
          "type": "string",
          "description": "hub sink output name",
          "default&quot;: &quot;inferenceOutput"
        },
        {
          "name": "motionSensitivity",
          "type": "string",
          "description": "motion detection sensitivity",
          "default&quot;: &quot;medium"
        },
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtsp source password.",
          "default&quot;: &quot;dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description&quot;: &quot;rtsp Url"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtsp source user name.",
          "default&quot;: &quot;dummyUserName"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.VideoAnalyzer.RtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password&quot;: &quot;${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "${motionSensitivity}",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.SignalGateProcessor",
          "activationEvaluationWindow": "PT1S",
          "activationSignalOffset": "PT0S",
          "minimumActivationTime": "PT30S",
          "maximumActivationTime": "PT30S",
          "name": "signalGateProcessor",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            },
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "${hubSinkOutputName}",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        },
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048",
          "videoName": "sample-motion-video-camera001",
          "videoCreationProperties": {
            "title": "sample-motion-video-camera001",
            "description": "Motion-detection based recording of clips to a video resource",
            "segmentLength&quot;: &quot;PT30S"
          },
          "name": "videoSink",
          "inputs": [
            {
              "nodeName": "signalGateProcessor",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value&quot;: &quot;video"
                }
              ]
            }
          ]
        }
      ]
    }
  }
}
```

Beachten Sie die folgenden Eigenschaften in der Antwortnutzlast:

* Der Statuscode lautet „200“ und gibt somit die erfolgreiche Ausführung an.
* Die Nutzlast enthält die beiden Zeitstempel `createdAt` und `lastModifiedAt`.

### <a name="create-a-live-pipeline-using-the-topology"></a>Erstellen einer Livepipeline mithilfe der Topologie

Erstellen Sie als Nächstes eine Livepipeline, die auf die oben genannte Pipelinetopologie verweist. Rufen Sie die direkte Methode `livePipelineSet` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1",
    "properties" : {
        "topologyName" : "EVRtoVideoSinkOnMotionDetection",
        "description" : "Sample pipeline description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/lots_015.mkv" }
        ]
    }
}
```

Beachten Sie Folgendes:

* Die oben genannte Nutzlast gibt die Topologie („EVRtoVideoSinkOnMotionDetection“) an, die von der Livepipeline verwendet werden soll.
* Die Nutzlast enthält den Parameterwert für `rtspUrl`, da die Topologienutzlast dafür keinen Standardwert enthält.

Innerhalb weniger Sekunden wird die folgende Antwort im Ausgabefenster angezeigt:

```
[DirectMethod] Invoking Direct Method [livePipelineSet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T15:20:29.023Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T15:20:29.023Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Beachten Sie die folgenden Eigenschaften in der Antwortnutzlast:

* Der Statuscode lautet „201“. Hierdurch wird angegeben, dass eine neue Livepipeline erstellt wurde.
* Der Status ist „Inactive“ und gibt somit an, dass die Livepipeline erstellt, jedoch nicht aktiviert wurde. Weitere Informationen finden Sie unter [Pipelinezustände](../pipeline.md#pipeline-states).

Probieren Sie als nächste Schritte die folgenden direkten Methoden aus:

* Rufen Sie wieder `livePipelineSet` mit der gleichen Nutzlast auf. Sie werden feststellen, dass nun „200“ als Statuscode zurückgegeben wird.
* Rufen Sie erneut `livePipelineSet` auf, jedoch mit einer anderen Beschreibung. Beachten Sie die aktualisierte Beschreibung in der Antwortnutzlast, durch die angegeben wird, dass die Livepipeline erfolgreich aktualisiert wurde.

    > [!NOTE]
    > Wie unter [Pipelinetopologien](../pipeline.md#pipeline-topologies) erläutert, können Sie mehrere Livepipelines erstellen, um Livevideostreams von vielen Kameras mit derselben Pipelinetopologie zu analysieren. In dieser speziellen Topologie ist der Wert von `videoName` allerdings hartcodiert. Da nur eine Livevideoquelle in einer Video Analyzer-Videoressource aufgezeichnet werden soll, dürfen Sie keine zusätzlichen Livepipelines mit dieser speziellen Topologie erstellen.

### <a name="activate-the-live-pipeline"></a>Aktivieren der Livepipeline

Als Nächstes können Sie die Livepipeline aktivieren, die den Flow des (simulierten) Livevideos über die Pipeline startet. Rufen Sie die direkte Methode `livePipelineActivate` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden.

```
[DirectMethod] Invoking Direct Method [livePipelineActivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ in der Antwortnutzlast gibt an, dass die Livepipeline erfolgreich aktiviert wurde.

### <a name="check-the-state-of-the-live-pipeline"></a>Überprüfen des Status der Livepipeline

Rufen Sie jetzt die direkte Methode `livePipelineGet` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden.

```
[DirectMethod] Invoking Direct Method [livePipelineGet] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-05-03T14:21:21.750Z",
      "lastModifiedAt&quot;: &quot;2021-05-03T14:21:21.750Z"
    },
    "name": "Sample-Pipeline-1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "EVRtoVideoSinkOnMotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value&quot;: &quot;rtsp://rtspsim:554/media/lots_015.mkv"
        }
      ]
    }
  }
}
```

Beachten Sie die folgenden Eigenschaften in der Antwortnutzlast:

* Der Statuscode lautet „200“ und gibt somit die erfolgreiche Ausführung an.
* Der Status „Active“ gibt an, dass sich die Livepipeline nun im Zustand „Aktiv“ befindet.

## <a name="observe-results"></a>Überprüfen der Ergebnisse

In der zuvor erstellten und aktivierten Livepipeline wird der Knoten des Bewegungserkennungsprozessors verwendet, um Bewegungen im eingehenden Livevideostream zu erkennen. Dann werden Ereignisse an die IoT Hub-Senke gesendet. Diese Ereignisse werden anschließend als Meldungen an den IoT Hub weitergeleitet. Dies lässt sich nun beobachten. Im Ausgabefenster werden die folgenden Nachrichten angezeigt.

```
[IoTHubMonitor] [1:22:53 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620066173760872 1 IN IP4 172.18.0.4\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=Z2QAKKzZQHgCJoQAAAMABAAAAwDwPGDGWA==,aOvhEsiw\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-03T18:22:53.761Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066173816,
    "iothub-message-source": "Telemetry",
    "messageId": "c2de6a40-1e0a-45ef-9449-599fc5680d05",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
[IoTHubMonitor] [1:22:59 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "timestamp": 145805956115743,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.48954,
            "t": 0.140741,
            "w": 0.075,
            "h": 0.058824
          }
        }
      }
    ]
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/motionDetection",
    "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
    "eventTime": "2021-05-03T18:22:59.063Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066179359,
    "iothub-message-source": "Telemetry",
    "messageId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}

```

Beachten Sie in diesen Nachrichten die folgenden Eigenschaften:

* Jede Nachricht enthält die beiden Abschnitte `body` und `properties`. Informationen zu diesen Abschnitten finden Sie im Artikel [Erstellen und Lesen von IoT Hub-Nachrichten](../../../iot-hub/iot-hub-devguide-messages-construct.md).
* Die erste Nachricht ist **MediaSessionEstablished** und gibt an, dass der Knoten der RTSP-Quelle (subject) eine Verbindung mit dem RTSP-Simulator herstellen und der Empfang eines (simulierten) Livefeeds beginnen konnte.
* In `subject` wird auf den Knoten in der Livepipeline verwiesen, von dem die Nachricht generiert wurde. In diesem Fall stammt die Nachricht vom RTSP-Quellknoten.
* `eventType` gibt an, dass es sich um ein Diagnoseereignis handelt.
* Die `eventTime` gibt den Zeitpunkt des Ereignisses an.
* `body` enthält Daten zum Ereignis, d. h. die [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Nachricht.
* Die zweite Nachricht betrifft ein **Rückschluss**-Ereignis. Sie können überprüfen, ob die Nachricht ungefähr 5 Sekunden nach der Nachricht **MediaSessionEstablished** gesendet wird. Dies entspricht der Verzögerung zwischen dem Start des Videos und dem Zeitpunkt, zu dem das Fahrzeug durch den Parkplatz fährt.
* `subject` verweist auf den Knoten des Bewegungserkennungsprozessors in der Pipeline, über den die Nachricht generiert wird.
* Dabei handelt es sich um ein Analyseereignis, sodass `body` `timestamp`- und `inferences`-Daten enthält.
* Der Abschnitt `inferences` gibt an, dass `type` den Wert „motion“ aufweist, und enthält zusätzliche Daten zum Ereignis „motion“.

Beachten Sie, dass der Abschnitt `messageId` im Textkörper „9ccfab80-2993-42c7-9452-92e21df96413“ lautet. Dies wird im folgenden Betriebsereignis angezeigt:

```
[IoTHubMonitor] [1:23:31 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion-video-camera001"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "eventTime": "2021-05-03T18:23:31.319Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066211373,
    "iothub-message-source": "Telemetry",
    "messageId": "c7cbb363-7cc7-4169-936f-55de5fae111c",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
" Diese **RecordingStarted**-Nachricht gibt an, dass die Videoaufzeichnung gestartet wurde. Beachten Sie, dass der `correlationId`-Wert „9ccfab80-2993-42c7-9452-92e21df96413“ mit der `messageId` der **Rückschlussmeldung** übereinstimmt, sodass Sie nachverfolgen können, welches Ereignis die Aufzeichnung ausgelöst hat. Das nächste Betriebsereignis sieht wie folgt aus:

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingAvailable",
    "eventTime": "2021-05-03T18:24:00.686Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240741,
    "iothub-message-source": "Telemetry",
    "messageId": "5b26aa88-e037-4834-af34-a6a4df3c42c2",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
```

Dieses **RecordingAvailable**-Ereignis gibt an, dass die Mediendaten jetzt in der Videoressource aufgezeichnet wurden. Beachten Sie, dass `correlationId` identisch ist: „9ccfab80-2993-42c7-9452-92e21df96413“. Das letzte Betriebsereignis für diese Nachrichtenkette (mit derselben `correlationId`) lautet wie folgt:

```
[IoTHubMonitor] [1:24:00 PM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
    "outputType": "video",
    "outputLocation&quot;: &quot;sample-motion"
  },
  "properties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/videoAnalyzers/{ava-account-name}",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/videoSink",
    "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStopped",
    "eventTime": "2021-05-03T18:24:00.710Z",
    "dataVersion&quot;: &quot;1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-connection-auth-method": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id": "637556611958535962",
    "iothub-enqueuedtime": 1620066240766,
    "iothub-message-source": "Telemetry",
    "messageId": "f3dbd5d5-3176-4d5b-80d8-c67de85bc619",
    "correlationId": "9ccfab80-2993-42c7-9452-92e21df96413",
    "contentType": "application/json",
    "contentEncoding&quot;: &quot;utf-8"
  }
}
```
Dieses **RecordingStopped**-Ereignis gibt an, dass das Signalgate geschlossen wurde und der relevante Teil des eingehenden Livevideos aufgezeichnet wurde. Beachten Sie, dass `correlationId` identisch ist: „9ccfab80-2993-42c7-9452-92e21df96413“.

In der Topologie wurde der Knoten des Signalgateprozessors mit Aktivierungszeiten von 30 Sekunden konfiguriert. Das heißt, dass die Pipelinetopologie Videoclips mit einer Länge von ungefähr 30 Sekunden aufzeichnet.  Während der Aufzeichnung des Videos gibt der Knoten des Bewegungserkennungsprozessors weiterhin **Rückschluss**-Ereignisse aus, die im Ausgabefenster zwischen den Ereignissen **RecordingAvailable** und **RecordingStopped** angezeigt werden.

Wenn die Livepipeline weiterhin ausgeführt wird, erreicht der RTSP-Simulator das Ende der Videodatei und beendet bzw. trennt die Verbindung. Der Knoten der RTSP-Quelle stellt dann erneut eine Verbindung mit dem Simulator her, und der Prozess wird wiederholt.
    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Aufrufen zusätzlicher direkter Methoden zur Bereinigung

Als Nächstes können Sie direkte Methoden aufrufen, um die Livepipeline (in dieser Reihenfolge) zu deaktivieren und zu löschen.

### <a name="deactivate-the-live-pipeline"></a>Deaktivieren der Livepipeline

Rufen Sie die direkte Methode `livePipelineDeactivate` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name" : "Sample-Pipeline-1"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden.

```
[DirectMethod] Invoking Direct Method [livePipelineDeactivate] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich deaktiviert wurde.


### <a name="delete-the-live-pipeline"></a>Löschen der Livepipeline

Rufen Sie die direkte Methode `livePipelineDelete` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;Sample-Pipeline-1"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden:

```
[DirectMethod] Invoking Direct Method [livePipelineDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ in der Antwort gibt an, dass die Livepipeline erfolgreich gelöscht wurde.


### <a name="delete-the-pipeline-topology"></a>Löschen der Pipelinetopologie

Rufen Sie die direkte Methode `pipelineTopologyDelete` mit der folgenden Nutzlast auf:

```
{
    "@apiVersion" : "1.1",
    "name&quot; : &quot;EVRtoVideoSinkOnMotionDetection"
}
```

Innerhalb weniger Sekunden sollte die folgende Antwort im Ausgabefenster angezeigt werden.

```
[DirectMethod] Invoking Direct Method [pipelineTopologyDelete] to [avasample-iot-edge-device/avaedge] ...
[DirectMethod] Response from [avasample-iot-edge-device/avaedge]:
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Pipelinetopologie erfolgreich deaktiviert wurde.

## <a name="playing-back-the-recording"></a>Wiedergeben der Aufzeichnung

Sie können die von der Livepipeline erstellte Video Analyzer-Videoressource untersuchen, indem Sie sich beim Azure-Portal anmelden und das Video anzeigen.
1. Öffnen Sie Ihren Webbrowser, und navigieren Sie zum [Azure-Portal](https://portal.azure.com/). Geben Sie Ihre Anmeldeinformationen ein, um sich beim Portal anzumelden. Die Standardansicht ist Ihr Dienstdashboard.
1. Suchen Sie unter den Ressourcen Ihres Abonnements nach Ihrem Video Analyzer-Konto, und öffnen Sie den Kontobereich.
1. Wählen Sie in der Liste **Video Analyzer** die Option **Videos** aus.
1. Ein Video mit dem Namen `sample-motion-video-camera001` ist aufgeführt. Dies ist der Name, den Sie in Ihrer Datei mit der Pipelinetopologie ausgewählt haben.
1. Wählen Sie das Video aus.
1. Die Seite mit den Videodetails wird geöffnet, und die Wiedergabe sollte automatisch gestartet werden.

    <!--TODO: add image -- ![Video playback]() TODO: new screenshot is needed here -->


[!INCLUDE [activate-deactivate-pipeline](./includes/common-includes/activate-deactivate-pipeline.md)]    

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Wiedergabe von Videoaufzeichnungen](../playback-recordings-how-to.md).
* Probieren Sie die [Schnellstartanleitung zum Analysieren von Livevideos](analyze-live-video-use-your-model-http.md) aus.
