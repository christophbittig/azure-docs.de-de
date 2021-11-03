---
title: 'Erste Schritte mit Azure Video Analyzer: Azure'
description: In dieser Schnellstartanleitung werden die ersten Schritte mit Azure Video Analyzer erläutert. Dabei werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2dcb4ef9717cbd754bf963015e0054b0c307738d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030007"
---
# <a name="quickstart-get-started-with-azure-video-analyzer"></a>Schnellstart: Erste Schritte mit Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

In dieser Schnellstartanleitung werden die ersten Schritte mit Azure Video Analyzer erläutert. Dabei werden ein virtueller Azure-Computer als IoT Edge-Gerät und ein simulierter Livevideostream verwendet.

Nach dem Ausführen der Setupschritte können Sie den simulierten Livevideostream über eine Pipeline ausführen, der Bewegungen im Stream erkennt und meldet. Diese Pipeline ist im folgenden Diagramm grafisch dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Bewegungserkennung":::

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    [!INCLUDE [azure-subscription-permissions](./includes/common-includes/azure-subscription-permissions.md)]
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:

    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

Der Bereitstellungsprozess dauert ungefähr **20 Minuten**. Nach Abschluss haben Sie bestimmte Azure-Ressourcen im Azure-Abonnement bereitgestellt. Hierzu zählen unter anderem:
1. **Video Analyzer-Konto:** Dieser [Clouddienst](../overview.md) wird zum Registrieren des Video Analyzer-Edgemoduls und zur Wiedergabe aufgezeichneter Videos und Videoanalysen verwendet.
1. **Speicherkonto:** Zum Speichern aufgezeichneter Videos und Videoanalysen.
1. **Verwaltete Identität**: Dies ist die benutzerseitig zugewiesene [verwaltete Identität](../../../active-directory/managed-identities-azure-resources/overview.md), die zum Verwalten des Zugriffs auf das obige Speicherkonto verwendet wird.
1. **Virtueller Computer:** Ein virtueller Computer, der als Ihr simuliertes Edgegerät dient.
1. **IoT Hub**: Dient als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung, IoT Edge-Modulen und den verwalteten Geräten.

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

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

## <a name="use-direct-method-calls"></a>Verwenden von Aufrufen direkter Methoden

Sie können jetzt Livevideostreams analysieren, indem Sie direkte Methoden aufrufen, die vom Video Analyzer-Edgemodul verfügbar gemacht werden. Lesen Sie [Direkte Methoden in Video Analyzer](direct-methods.md), um alle direkten Methoden zu untersuchen, die vom Modul bereitgestellt werden. Das Schema für die direkten Methoden finden Sie [hier](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json).

### <a name="enumerate-pipeline-topologies"></a>Aufzählen von Pipelinetopologien

In diesem Schritt werden alle [Pipelinetopologien](../pipeline.md) im Modul aufgelistet.

1. Klicken Sie mit der rechten Maustaste auf das Modul „avaedge“, und wählen Sie im Kontextmenü die Option **Invoke Module Direct Method** (Direkte Methode des Moduls aufrufen) aus.
1. Oben in der Mitte des Visual Studio Code-Fensters wird ein Bearbeitungsfeld angezeigt. Geben Sie `pipelineTopologyList` in das Bearbeitungsfeld ein, und drücken Sie die EINGABETASTE.
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

Anhand der gleichen Schritte wie oben können Sie `pipelineTopologySet` aufrufen, um eine Pipelinetopologie mit dem folgenden JSON-Code als Nutzlast festzulegen: Sie erstellen eine Pipelinetopologie mit dem Namen „MotionDetection“.


```json
{
    "@apiVersion": "1.1",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUrl",
                "type": "string",
                "description&quot;: &quot;rtspUrl"
            },
            {
                "name": "rtspUserName",
                "type": "string",
                "description": "rtspUserName",
                "default&quot;: &quot;dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "string",
                "description": "rtspPassword",
                "default&quot;: &quot;dummypw"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.VideoAnalyzer.RtspSource",
                "name": "rtspSource",
                "transport": "tcp",
                "endpoint": {
                    "@type": "#Microsoft.VideoAnalyzer.UnsecuredEndpoint",
                    "credentials": {
                        "@type": "#Microsoft.VideoAnalyzer.UsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password&quot;: &quot;${rtspPassword}"
                    },
                    "url&quot;: &quot;${rtspUrl}"
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
                "sensitivity": "medium",
                "name": "motionDetection",
                "inputs": [
                    {
                        "nodeName": "rtspSource",
                        "outputSelectors": []
                    }
                ]
            }
        ],
        "sinks": [
            {
                "hubOutputName": "inferenceOutput",
                "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
                "name": "iotHubSink",
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

Diese JSON-Nutzlast erstellt eine Topologie, die drei Parameter definiert, wobei zwei von ihnen Standardwerte aufweisen. Die Topologie enthält einen Quellknoten ([RTSP-Quelle](../pipeline.md#rtsp-source)), einen Prozessorknoten ([Bewegungserkennungsprozessor](../pipeline.md#motion-detection-processor)) und einen Senkenknoten ([IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink)). Die visuelle Darstellung der Topologie ist oben abgebildet.

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

Als Statuscode wird „201“ zurückgegeben. Dieser Status gibt an, dass eine neue Topologie erstellt wurde.

Versuchen Sie nun, diese nächsten Schritte auszuführen:

1. Rufen Sie `pipelineTopologySet` erneut auf. Als Statuscode wird „200“ zurückgegeben. Dieser Code gibt an, dass eine vorhandene Topologie erfolgreich aktualisiert wurde.
1. Rufen Sie `pipelineTopologySet` erneut auf, aber ändern Sie nun die Beschreibungszeichenfolge. Als Statuscode wird „200“ zurückgegeben, und die Beschreibung wird auf den neuen Stand aktualisiert.
1. Rufen Sie `pipelineTopologyList` wie im vorherigen Abschnitt beschrieben auf. Die Topologie „MotionDetection“ wird jetzt in der zurückgegebenen Nutzlast angezeigt.

### <a name="read-the-pipeline-topology"></a>Lesen der Pipelinetopologie

Rufen Sie `pipelineTopologyGet` auf, indem Sie die folgende Nutzlast verwenden.

```json
{
    "@apiVersion" : "1.1",
    "name" : "MotionDetection"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:16:46.491Z",
      "lastModifiedAt": "2021-03-21T18:16:46.491Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspPassword",
          "type": "string",
          "description": "rtspPassword",
          "default": "dummypw"
        },
        {
          "name": "rtspUrl",
          "type": "string",
          "description": "rtspUrl"
        },
        {
          "name": "rtspUserName",
          "type": "string",
          "description": "rtspUserName",
          "default": "dummyUserName"
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
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.VideoAnalyzer.MotionDetectionProcessor",
          "sensitivity": "medium",
          "eventAggregationWindow": "PT1S",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.IotHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "iotHubSink",
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

Beachten Sie in der Antwortnutzlast die folgenden Details:

* Der Statuscode lautet „200“ (erfolgreiche Ausführung).
* Die Nutzlast enthält die beiden Zeitstempel `createdAt` und `lastModifiedAt`.

### <a name="create-a-live-pipeline-using-the-topology"></a>Erstellen einer Livepipeline mithilfe der Topologie

Erstellen Sie als Nächstes eine Livepipeline, die auf die oben genannte Pipelinetopologie verweist. Rufen Sie die direkte Methode `livePipelineSet` mit der folgenden Nutzlast auf:

```json
{
    "@apiVersion" : "1.1",
    "name": "mdpipeline1",
    "properties": {
        "topologyName": "MotionDetection",
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

Beachten Sie, dass für diese Nutzlast Folgendes gilt:

* Die oben genannte Nutzlast gibt die Topologie („MotionDetection“) an, die von der Livepipeline verwendet werden soll.
* Die Nutzlast enthält den Parameterwert für `rtspUrl`, da die Topologienutzlast dafür keinen Standardwert enthält. Dieser Wert ist ein Link zum folgenden Beispielvideo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]


Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```json
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Inactive",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Beachten Sie in der Antwortnutzlast Folgendes:

* Der Statuscode lautet „201“. Hierdurch wird angegeben, dass eine neue Livepipeline erstellt wurde.
* Der Status ist „Inactive“ und gibt somit an, dass die Livepipeline erstellt, jedoch nicht aktiviert wurde. Weitere Informationen finden Sie unter [Pipelinezustände](../pipeline.md#pipeline-states).

Probieren Sie als nächste Schritte die folgenden direkten Methoden aus:

* Rufen Sie wieder `livePipelineSet` mit der gleichen Nutzlast auf. Sie werden feststellen, dass nun „200“ als Statuscode zurückgegeben wird.
* Rufen Sie erneut `livePipelineSet` auf, jedoch mit einer anderen Beschreibung. Beachten Sie die aktualisierte Beschreibung in der Antwortnutzlast, durch die angegeben wird, dass die Livepipeline erfolgreich aktualisiert wurde.
* Rufen Sie `livePipelineSet` auf, ändern Sie jedoch den Namen in „mdpipeline2“ und `rtspUrl` in „rtsp://rtspsim:554/media/lots_015.mkv“. Beachten Sie in der Antwortnutzlast die neu erstellte Livepipeline (Statuscode „201“).
    > [!NOTE]
    > Wie unter [Pipelinetopologien](../pipeline.md#pipeline-topologies) erläutert, können Sie mehrere Livepipelines erstellen, um Livevideostreams von vielen Kameras mit derselben Pipelinetopologie zu analysieren. Wenn Sie zusätzliche Livepipelines erstellen, denken Sie daran, diese im Bereinigungsschritt zu löschen.

### <a name="activate-the-live-pipeline"></a>Aktivieren der Livepipeline

Als Nächstes können Sie die Livepipeline aktivieren, die den Flow des (simulierten) Livevideos über die Pipeline startet. Rufen Sie die direkte Methode `livePipelineActivate` mit der folgenden Nutzlast auf:

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

Nach einigen Sekunden wird im Ausgabefenster die folgende Antwort angezeigt.

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich aktiviert wurde.

### <a name="check-the-state-of-the-live-pipeline"></a>Überprüfen des Status der Livepipeline

Rufen Sie jetzt die direkte Methode `livePipelineGet` mit der folgenden Nutzlast auf:

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

Nach einigen Sekunden wird im Ausgabefenster die folgende Antwort angezeigt.

```json
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2021-03-21T18:27:41.639Z",
      "lastModifiedAt": "2021-03-21T18:27:41.639Z"
    },
    "name": "mdpipeline1",
    "properties": {
      "state": "Active",
      "description": "Sample pipeline description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspPassword",
          "value": "testpassword"
        },
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        },
        {
          "name": "rtspUserName",
          "value": "testuser"
        }
      ]
    }
  }
}
```

Beachten Sie in der Antwortnutzlast die folgenden Details:

* Der Statuscode lautet „200“ (erfolgreiche Ausführung).
* Der Status „Active“ gibt an, dass sich die Livepipeline nun im aktiven Status befindet.

## <a name="observe-results"></a>Überprüfen der Ergebnisse

In der zuvor erstellten und aktivierten Livepipeline wird der Knoten des Bewegungserkennungsprozessors verwendet, um Bewegungen im eingehenden Livevideostream zu erkennen. Dann werden Ereignisse an die IoT Hub-Senke gesendet. Diese Ereignisse werden anschließend als Meldungen an den IoT Hub weitergeleitet. Dies lässt sich nun beobachten. Im Ausgabefenster werden Meldungen mit folgendem „body“ angezeigt:


```json
{
  "timestamp": 145471641211899,
  "inferences": [
    {
      "type": "motion",
      "motion": {
        "box": {
          "l": 0.514644,
          "t": 0.574627,
          "w": 0.3375,
          "h": 0.096296
        }
      }
    }
  ]
}
```

Beachten Sie dieses Detail:

*   Der Rückschlussabschnitt gibt an, dass der Typ „Bewegung“ ist. Er stellt zusätzliche Daten zum Bewegungsereignis und einen Begrenzungsrahmen für den Bereich des Videoframes (zum angegebenen Zeitstempel) bereit, in dem Bewegung erkannt wurde.

    
## <a name="invoke-additional-direct-method-calls-to-clean-up"></a>Aufrufen zusätzlicher direkter Methoden zur Bereinigung

Als Nächstes können Sie direkte Methoden aufrufen, um die Livepipeline (in dieser Reihenfolge) zu deaktivieren und zu löschen.

### <a name="deactivate-the-live-pipeline"></a>Deaktivieren der Livepipeline

Rufen Sie die direkte Methode `livePipelineDeactivate` mit der folgenden Nutzlast auf:

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich deaktiviert wurde. 

Versuchen Sie anschließend, `livePipelineGet` aufzurufen (wie oben in diesem Artikel beschrieben). Beobachten Sie den Zustandswert.

### <a name="delete-the-live-pipeline"></a>Löschen der Livepipeline

Rufen Sie die direkte Methode `livePipelineDelete` mit der folgenden Nutzlast auf:

```json
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline1"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": null
}
```
Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich gelöscht wurde.

Wenn Sie auch die Pipeline „mdpipeline2“ erstellt haben, können Sie die Pipelinetopologie nicht löschen, ohne auch diese zusätzliche Pipeline zu löschen. Rufen Sie die direkte Methode `livePipelineDelete` erneut auf, indem Sie die folgende Nutzlast verwenden:

```
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline2"
}
```

Nach einigen Sekunden wird im Ausgabefenster die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich gelöscht wurde.

Sie können `livePipelineList` aufrufen, indem Sie dieselbe Nutzlast wie für `pipelineTopologyList` verwenden. Beachten Sie, dass keine Livepipelines aufzählt werden.

### <a name="delete-the-pipeline-topology"></a>Löschen der Pipelinetopologie

Nachdem alle Livepipelines gelöscht wurden, können Sie die direkte Methode `pipelineTopologyDelete` mit der folgenden Nutzlast aufrufen:

```json
{
    "@apiVersion" : "1.1",
    "name" : "MotionDetection"
}
```

Nach einigen Sekunden wird im **Ausgabefenster** die folgende Antwort angezeigt.

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Topologie erfolgreich gelöscht wurde.

Sie können versuchen, `pipelineTopologyList` aufzurufen. Sie sehen, dass das Modul keine Topologien enthält.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie die [Schnellstartanleitung zum Aufzeichnen von Videos in der Cloud aus, wenn Bewegung erkannt wird](detect-motion-record-video-clips-cloud.md) aus.
* Probieren Sie die [Schnellstartanleitung zum Analysieren von Livevideos](analyze-live-video-use-your-model-http.md) aus.
* Informieren Sie sich über [Diagnosemeldungen](monitor-log-edge.md). 
