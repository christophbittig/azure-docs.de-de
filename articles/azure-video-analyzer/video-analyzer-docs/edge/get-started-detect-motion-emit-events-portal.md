---
title: Erste Schritte mit Azure Video Analyzer im Azure-Portal – Azure
description: In dieser Schnellstartanleitung werden die ersten Schritte mit Azure Video Analyzer im Azure-Portal erläutert.
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c39d6ddae6ad34ad705da8ee2b6de79a8102452
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398818"
---
# <a name="quickstart-get-started-with-video-analyzer-in-the-portal"></a>Schnellstart: Erste Schritte mit Video Analyzer im Portal

![Edgesymbol](media/env-icon/edge.png)  
Lesen Sie alternativ die Informationen unter [Schnellstart: Erste Schritte mit Video Analyzer-Livepipelines im Azure-Portal](../cloud/get-started-livepipelines-portal.md).

---

In dieser Schnellstartanleitung werden die ersten Schritte mit Azure Video Analyzer erläutert. Sie erstellen im Azure-Portal ein Azure Video Analyzer-Konto und die zugehörigen Ressourcen. Anschließend stellen Sie das Video Analyzer-Edgemodul und ein RTSP-Kamerasimulatormodul (Real-Time Streaming Protocol) auf Ihrem Azure IoT Edge-Gerät bereit.

Nach Abschluss der Einrichtungsschritte können Sie den simulierten Livevideostream über eine Pipeline ausführen, die Bewegungen im Stream erkennt und meldet. Diese Pipeline ist im folgenden Diagramm grafisch dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Diagramm: Pipeline, durch die Bewegung erkannt und gemeldet werden":::

## <a name="prerequisites"></a>Voraussetzungen

- Ein aktives Azure-Abonnement. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]

- Ein IoT Edge-Gerät, auf dem Sie über Administratorrechte verfügen:
  - [Bereitstellen eines IoT Edge-Geräts](deploy-iot-edge-device.md)
  - [Bereitstellen für IoT Edge für Linux unter Windows](deploy-iot-edge-linux-on-windows.md)
- [Visual Studio Code](https://code.visualstudio.com/) mit der Erweiterung [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

[!INCLUDE [install-docker-prompt](./includes/common-includes/install-docker-prompt.md)]

## <a name="prepare-your-iot-edge-device"></a>Vorbereiten Ihres IoT Edge-Geräts

Das Azure Video Analyzer-Modul muss für die Ausführung auf dem IoT Edge-Gerät mit einem nicht privilegierten lokalen Benutzerkonto konfiguriert werden. Für das Modul werden bestimmte lokale Ordner benötigt, um die Daten für die Anwendungskonfiguration zu speichern. Für das RTSP-Kamerasimulatormodul werden Videodateien benötigt, mit denen ein Livevideofeed synthetisiert werden kann.

Führen Sie auf Ihrem IoT Edge-Gerät den folgenden Befehl aus:

`bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`

Das Skript „prep-device“ in diesem Befehl automatisiert das Erstellen von Eingabe- und Konfigurationsordnern, das Herunterladen von Videoeingabedateien und das Erstellen von Benutzerkonten mit den richtigen Berechtigungen. Nach erfolgreichem Abschluss des Befehls sollten auf Ihrem Edgegerät die folgenden Ordner erstellt worden sein:

- _/home/localedgeuser/samples_
- _/home/localedgeuser/samples/input_
- _/var/lib/videoanalyzer_
- _/var/media_

Die Videodateien (MKV-Dateien) im Ordner */home/localedgeuser/samples/input\* werden zum Simulieren des Livevideos verwendet.

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Im nächsten Schritt müssen die erforderlichen Azure-Ressourcen (Video Analyzer-Konto, Speicherkonto, benutzerseitig zugewiesene verwaltete Identität) erstellt werden. Anschließend erstellen Sie eine optionale Containerregistrierung und registrieren ein Video Analyzer-Edgemodul beim Video Analyzer-Konto.

Wenn Sie ein Video Analyzer-Konto erstellen, müssen Sie es einem Azure-Speicherkonto zuordnen. Wenn Sie Video Analyzer verwenden, um das Live-Video von einer Kamera aufzuzeichnen, werden diese Daten als Blobs in einem Container im Speicherkonto gespeichert. Sie müssen eine verwaltete Identität verwenden, um dem Video Analyzer-Konto wie folgt den entsprechenden Zugriff auf das Speicherkonto zu gewähren.

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Erstellen eines Video Analyzer-Kontos im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Geben Sie auf der Suchleiste im oberen Bereich **Video Analyzer** ein.
1. Wählen Sie unter **Dienste** die Option **Video Analyzer** aus.
1. Wählen Sie **Hinzufügen**.
1. Geben Sie im Abschnitt **Create Video Analyzer account** (Video Analyzer-Konto erstellen) die folgenden erforderlichen Werte ein:

   - **Abonnement**: Wählen Sie das Abonnement aus, das Sie zum Erstellen des Video Analyzer-Kontos verwenden möchten.
   - **Ressourcengruppe**: Wählen Sie eine Ressourcengruppe aus, in der Sie das Video Analyzer-Konto erstellen möchten, oder wählen Sie **Neu erstellen** aus, um eine Ressourcengruppe zu erstellen.
   - **Video Analyzer account name** (Name des Video Analyzer-Kontos): Geben Sie einen Namen für Ihr Video Analyzer-Konto ein. Der Name darf nur aus drei bis 24 Kleinbuchstaben oder Zahlen bestehen und keine Leerzeichen enthalten.
   - **Standort**: Wählen Sie einen Standort zum Bereitstellen Ihres Video Analyzer-Kontos aus (beispielsweise **USA, Westen 2**).
   - **Speicherkonto**: Erstellen Sie ein Speicherkonto. Es wird empfohlen, ein Speicherkonto vom Typ [Standard „Allgemein v2“](../../../storage/common/storage-account-overview.md#types-of-storage-accounts) auszuwählen.
   - **Benutzeridentität**: Erstellen Sie eine neue benutzerseitig zugewiesene verwaltete Identität, und geben Sie ihr einen Namen.

1. Wählen Sie am unteren Rand des Formulars die Option **Überprüfen und erstellen** aus.

### <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

1. Klicken Sie auf **Ressource erstellen** > **Container** > **Container Registry**.
1. Geben Sie auf der Registerkarte **Allgemeine Informationen** Werte für **Ressourcengruppe** und **Registrierungsname** ein. Wählen Sie die Ressourcengruppe aus den vorherigen Abschnitten aus. Der Registrierungsname muss innerhalb von Azure eindeutig sein und fünf bis 50 alphanumerische Zeichen umfassen.
1. Übernehmen Sie für die übrigen Einstellungen die Standardwerte. Wählen Sie dann **Überprüfen + erstellen** aus. Überprüfen Sie die Einstellungen, und wählen Sie anschließend **Erstellen** aus.

## <a name="deploy-edge-modules"></a>Bereitstellen von Edgemodulen

### <a name="deploy-the-video-analyzer-edge-module"></a>Bereitstellen des Video Analyzer-Edgemoduls

1. Navigieren Sie zu Ihrem Video Analyzer-Konto.
1. Wählen Sie im Bereich **Edge** die Option **Edgemodule** aus.
1. Wählen Sie **Add edge modules** (Edgemodule hinzufügen) aus, geben Sie **avaedge** als Name für das neue Edgemodul ein, und wählen Sie anschließend **Hinzufügen** aus.
1. Rechts auf dem Bildschirm wird die Seite zum **Kopieren des Bereitstellungstokens** angezeigt. Kopieren Sie den Codeausschnitt unter **Recommended desired properties for IoT module deployment** (Empfohlene gewünschte Eigenschaften für die IoT-Modulbereitstellung). Sie benötigen sie in einem späteren Schritt.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Navigieren Sie zu Ihrem Azure IoT Hub-Konto.
1. Wählen Sie unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.
1. Wählen Sie unter **Geräte-ID** den Wert für Ihr IoT Edge-Gerät aus.
1. Wählen Sie **Module festlegen** aus.
1. Wählen Sie **Hinzufügen** und anschließend im Dropdownmenü die Option **IoT Edge-Modul** aus.
1. Geben Sie unter **Name des IoT Edge-Moduls** die Zeichenfolge **avaedge** ein.
1. Kopieren Sie die folgende Zeile, und fügen Sie sie in das Feld **Image-URI** ein: `mcr.microsoft.com/media/video-analyzer:1`.
1. Legen Sie **Umgebungsvariablen** fest.
1. Geben Sie unter **NAME** die Zeichenfolge **LOCAL_USER_ID** ein. Geben Sie unter **VALUE** den Wert **1010** ein.
1. Geben Sie in der zweiten Zeile unter **NAME** die Zeichenfolge **LOCAL_GROUP_ID** ein. Geben Sie unter **VALUE** den Wert **1010** ein.
1. Wählen Sie **Optionen für Containererstellung** aus. Kopieren Sie die folgenden Zeilen, und fügen Sie sie ein:
   ```json
   {
     "HostConfig": {
       "LogConfig": {
         "Type": "",
         "Config": {
           "max-size": "10m",
           "max-file": "10"
         }
       },
       "Binds": [
         "/var/media/:/var/media/",
         "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
       ],
       "IpcMode": "host",
       "ShmSize": 1536870912
     }
   }
   ```
1. Wählen Sie **Einstellungen für Modulzwilling** aus, und fügen Sie den Codeausschnitt ein, den Sie zuvor im Video Analyzer-Konto auf der Seite zum Kopieren des Bereitstellungstokens kopiert haben.
   ```JSON
     {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "XXXXXXX",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
     }
   ```
1. Wählen Sie am unteren Bildschirmrand die Option **Hinzufügen** aus.
1. Wählen Sie die Option **Routen**.
1. Geben Sie unter **NAME** die Zeichenfolge **AVAToHub** ein. Geben Sie unter **VALUE** den Wert `FROM /messages/modules/avaedge/outputs/* INTO $upstream` ein.
1. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus, um das Edgemodul **avaedge** bereitzustellen.

### <a name="deploy-the-edge-module-for-the-rtsp-camera-simulator"></a>Bereitstellen des Edgemoduls für den RTSP-Kamerasimulator

1. Navigieren Sie zu Ihrem IoT Hub-Konto.
1. Wählen Sie unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.
1. Wählen Sie unter **Geräte-ID** den Wert für Ihr IoT Edge-Gerät aus.
1. Wählen Sie **Module festlegen** aus.
1. Wählen Sie **Hinzufügen** und anschließend im Dropdownmenü die Option **IoT Edge-Modul** aus.
1. Geben Sie unter **Name des IoT Edge-Moduls** die Zeichenfolge **rtspsim** ein.
1. Kopieren Sie die folgende Zeile, und fügen Sie sie in das Feld **Image-URI** ein: `mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2`.
1. Wählen Sie **Optionen für Containererstellung** aus. Kopieren Sie die folgenden Zeilen, und fügen Sie sie ein:
   ```json
   {
     "HostConfig": {
       "Binds": ["/home/localedgeuser/samples/input:/live/mediaServer/media"]
     }
   }
   ```
1. Wählen Sie am unteren Bildschirmrand die Option **Hinzufügen** aus.
1. Wählen Sie **Überprüfen und erstellen** und anschließend **Erstellen** aus, um das Edgemodul **rtspsim** bereitzustellen.

### <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

Vergewissern Sie sich auf der Seite mit den Gerätedetails, dass die Module **avaedge** und **rtspsim** als **In Bereitstellung angegeben** und **Vom Gerät gemeldet** aufgeführt sind.

Unter Umständen dauert es einen Moment, bis die Module auf dem Gerät gestartet und an IoT Hub gemeldet wurden. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen. Der Statuscode **200 -- OK** bedeutet, dass die [IoT Edge-Runtime](../../../iot-edge/iot-edge-runtime.md) fehlerfrei ist und ordnungsgemäß funktioniert.

![Screenshot: Statuswert für eine IoT Edge-Runtime](./media/deploy-iot-edge-device/status.png)

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

### <a name="obtain-your-iot-hub-connection-string"></a>Abrufen der IoT Hub-Verbindungszeichenfolge

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub-Konto.
1. Suchen Sie im linken Bereich nach **Richtlinien für gemeinsamen Zugriff**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie die Richtlinie **iothubowner** aus.
1. Kopieren Sie den Wert unter **Primäre Verbindungszeichenfolge**. Er sieht wie folgt aus: `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-to-iot-hub"></a>Herstellen einer Verbindung mit IoT Hub

1. Öffnen Sie Visual Studio Code, und wählen Sie **Ansicht** > **Explorer** aus. Oder drücken Sie STRG+UMSCHALT+E.
1. Wählen Sie unten links auf der Registerkarte **Explorer** die Option **Azure IoT Hub** aus.
1. Wählen Sie das Symbol **Weitere Optionen** aus, um das Kontextmenü anzuzeigen. Wählen Sie anschließend **IoT Hub-Verbindungszeichenfolge festlegen** aus.
1. Geben Sie Ihre IoT Hub-Verbindungszeichenfolge ein, wenn das entsprechende Eingabefeld angezeigt wird.
1. Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich. Es sollte die Geräte-ID angezeigt werden, für die die folgenden Module bereitgestellt sind:

   - Video Analyzer-Edgemodul (Modulname **avaedge**)
   - RTSP-Simulator (Modulname **rtspsim**)

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/modules-node.png" alt-text="Screenshot: Erweiterter Modulknoten":::

> [!TIP]
> Falls Sie [Video Analyzer manuell auf einem Edgegerät (z. B. einem ARM64-Gerät) bereitgestellt haben](deploy-iot-edge-device.md), wird das Modul unter diesem Gerät (unter „Azure IoT Hub“) angezeigt. Sie können dieses Modul auswählen und mit den folgenden Schritten fortfahren.

### <a name="prepare-to-monitor-the-modules"></a>Vorbereiten der Überwachung der Module

Wenn Sie diese Schnellstartanleitung verwenden, werden Ereignisse an IoT Hub gesendet. Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code die Registerkarte **Erweiterungen** (oder drücken Sie STRG+UMSCHALT+X), und suchen Sie nach **Azure IoT Hub**.
1. Klicken Sie mit der rechten Maustaste auf die IoT Hub-Erweiterung, und wählen Sie **Erweiterungseinstellungen** aus.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/extension-settings.png" alt-text="Screenshot: Auswählen von „Erweiterungseinstellungen“":::

1. Suchen Sie nach der Option **Show Verbose Message** (Ausführliche Meldung anzeigen), und aktivieren Sie sie.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/get-started-detect-motion-emit-events/verbose-message.png" alt-text="Screenshot: Aktivierte Option „Show Verbose Message“ (Ausführliche Meldung anzeigen)":::

1. Öffnen Sie in Visual Studio Code den Bereich **Explorer**, und suchen Sie links unten nach **Azure IoT Hub**.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf Ihre Geräte-ID, und wählen Sie **Überwachung des integrierten Ereignisendpunkts starten** aus.

   > [!NOTE]
   > Unter Umständen werden Sie aufgefordert, Informationen zum integrierten Endpunkt für IoT Hub anzugeben. Sie erhalten diese Informationen, indem Sie im Azure-Portal zu Ihrem IoT Hub-Konto navigieren und im linken Bereich nach **Integrierte Endpunkte** suchen. Wählen Sie die Option aus, und navigieren Sie zum Abschnitt **Event Hub-kompatibler Endpunkt**. Kopieren und verwenden Sie den im Feld enthaltenen Text. Der Endpunkt sieht in etwa wie folgt aus:
   >
   > ```
   > Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
   > ```

## <a name="use-direct-method-calls"></a>Verwenden von Aufrufen direkter Methoden

Sie können jetzt Livevideostreams analysieren, indem Sie direkte Methoden aufrufen, die durch das Video Analyzer-Edgemodul verfügbar gemacht werden. Unter [Direkte Methoden in Azure Video Analyzer](direct-methods.md) können Sie sich mit den bereitgestellten direkten Methoden des Moduls vertraut machen. Das Schema für die direkten Methoden finden Sie [hier](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/videoanalyzer/data-plane/VideoAnalyzer.Edge/preview/1.0.0/AzureVideoAnalyzerSdkDefinitions.json).

### <a name="enumerate-pipeline-topologies"></a>Aufzählen von Pipelinetopologien

In diesem Schritt werden alle [Pipelinetopologien](../pipeline.md) im Modul aufgelistet.

1. Klicken Sie mit der rechten Maustaste auf das Modul **avaedge**, und wählen Sie im Kontextmenü die Option **Invoke Module Direct Method** (Direkte Methode des Moduls aufrufen) aus.
1. Geben Sie **pipelineTopologyList** in das Bearbeitungsfeld ein, und drücken Sie die EINGABETASTE.
1. Kopieren Sie die folgenden JSON-Nutzdaten, fügen Sie sie in das Bearbeitungsfeld ein, und drücken Sie anschließend die EINGABETASTE.

   ```json
   {
     "@apiVersion": "1.1"
   }
   ```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

```
[DirectMethod] Invoking Direct Method [pipelineTopologyList] to [deviceId/avaedge] ...
[DirectMethod] Response from [deviceId/avaedge]:
{
  "status": 200,
  "payload": {
    "value": []
  }
}
```

Die Antwort entspricht den Erwartungen, da keine Pipelinetopologien erstellt wurden.

### <a name="set-a-pipeline-topology"></a>Festlegen einer Pipelinetopologie

Mithilfe der oben beschriebenen Schritte können Sie auch `pipelineTopologySet` aufrufen, um eine Pipelinetopologie festzulegen, und dabei den folgenden JSON-Code als Nutzdaten verwenden. Hier wird eine Pipelinetopologie mit dem Namen _MotionDetection_ erstellt.

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

Durch diese JSON-Nutzdaten wird eine Topologie erstellt, die drei Parameter definiert – zwei davon mit Standardwerten. Die Topologie enthält einen Quellknoten ([RTSP-Quelle](../pipeline.md#rtsp-source)), einen Prozessorknoten ([Bewegungserkennungsprozessor](../pipeline.md#motion-detection-processor)) und einen Senkenknoten ([IoT Hub-Nachrichtensenke](../pipeline.md#iot-hub-message-sink)). Die Nutzdaten zeigen die visuelle Darstellung der Topologie.

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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

- Rufen Sie `pipelineTopologySet` erneut auf. Als Statuscode wird „200“ zurückgegeben. Dieser Code gibt an, dass eine vorhandene Topologie erfolgreich aktualisiert wurde.
- Rufen Sie `pipelineTopologySet` erneut auf, aber ändern Sie nun die Beschreibungszeichenfolge. Als Statuscode wird „200“ zurückgegeben, und die Beschreibung wird auf den neuen Stand aktualisiert.
- Rufen Sie `pipelineTopologyList` wie im vorherigen Abschnitt beschrieben auf. Nun wird die Topologie _MotionDetection_ in den zurückgegebenen Nutzdaten angezeigt.

### <a name="read-the-pipeline-topology"></a>Lesen der Pipelinetopologie

Rufen Sie `pipelineTopologyGet` unter Verwendung der folgenden Nutzdaten auf:

```json
{
  "@apiVersion": "1.1",
  "name": "MotionDetection"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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

- Der Statuscode lautet „200“ (erfolgreiche Ausführung).
- Die Nutzlast enthält die beiden Zeitstempel `createdAt` und `lastModifiedAt`.

### <a name="create-a-live-pipeline-by-using-the-topology"></a>Erstellen einer Livepipeline mithilfe der Topologie

Erstellen Sie als Nächstes eine Livepipeline mit Verweis auf die obige Pipelinetopologie. Rufen Sie die direkte Methode `livePipelineSet` mit der folgenden Nutzlast auf:

```json
{
  "@apiVersion": "1.1",
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

- Sie gibt die Topologie (_MotionDetection_) an, die von der Livepipeline verwendet wird.
- Sie enthält einen Parameterwert für `rtspUrl`, da die Topologienutzdaten dafür keinen Standardwert enthalten. Dieser Wert ist ein Link zum folgenden Beispielvideo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4LTY4]

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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

- Der Statuscode 201 gibt an, dass eine neue Livepipeline erstellt wurde.
- Der Zustand `Inactive` gibt an, dass die Livepipeline erstellt, aber nicht aktiviert wurde. Weitere Informationen finden Sie unter [Pipelinezustände](../pipeline.md#pipeline-states).

Probieren Sie als nächste Schritte die folgenden direkten Methoden aus:

- Rufen Sie erneut `livePipelineSet` mit den gleichen Nutzdaten auf. Beachten Sie, dass nun der Statuscode 200 zurückgegeben wird.
- Rufen Sie erneut `livePipelineSet` auf, diesmal aber mit einer anderen Beschreibung. Beachten Sie die aktualisierte Beschreibung in den Nutzdaten der Antwort. Daran erkennen Sie, dass die Livepipeline erfolgreich aktualisiert wurde.
- Rufen Sie `livePipelineSet` auf, und ändern Sie den Namen in `mdpipeline2` sowie `rtspUrl` in `rtsp://rtspsim:554/media/lots_015.mkv`. Beachten Sie die neu erstellte Livepipeline (Statuscode 201) in den Nutzdaten der Antwort.

  > [!NOTE]
  > Wie unter [Pipelinetopologien](../pipeline.md#pipeline-topologies) erläutert, können Sie mehrere Livepipelines erstellen, um Livevideostreams von zahlreichen Kameras unter Verwendung der gleichen Pipelinetopologie zu analysieren. Sollten Sie weitere Livepipelines erstellen, denken Sie daran, sie im Bereinigungsschritt zu löschen.

### <a name="activate-the-live-pipeline"></a>Aktivieren der Livepipeline

Sie können nun die Livepipeline aktivieren, um den Flow des (simulierten) Livevideos durch die Pipeline zu startet. Rufen Sie die direkte Methode `livePipelineActivate` mit der folgenden Nutzlast auf:

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich aktiviert wurde.

### <a name="check-the-state-of-the-live-pipeline"></a>Überprüfen des Status der Livepipeline

Rufen Sie die direkte Methode `livePipelineGet` mit der folgenden Nutzlast auf:

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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

- Der Statuscode lautet „200“ (erfolgreiche Ausführung).
- Der Zustand `Active` gibt an, dass die Livepipeline nun aktiv ist.

## <a name="observe-results"></a>Überprüfen der Ergebnisse

In der von Ihnen erstellten und aktivierten Livepipeline wird der Knoten des Bewegungserkennungsprozessors verwendet, um Bewegungen im eingehenden Livevideostream zu erkennen. Dann werden Ereignisse an die IoT Hub-Senke gesendet. Diese Ereignisse werden anschließend als Meldungen an IoT Hub weitergeleitet. Dies lässt sich nun beobachten. Im Fenster **AUSGABE** werden Meldungen mit folgendem Inhalt angezeigt:

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

Im Abschnitt `inferences` ist der Typ angegeben – in diesem Fall: „motion“ (Bewegung). Er enthält weitere Daten zum Bewegungsereignis. Des Weiteren wird hier ein Begrenzungsrahmen für den Bereich des Videoframes (zum angegebenen Zeitstempel) bereitgestellt, in dem eine Bewegung erkannt wurde.

## <a name="invoke-more-direct-method-calls-to-clean-up"></a>Aufrufen weiterer direkter Methoden zur Bereinigung

Als Nächstes können Sie direkte Methoden aufrufen, um die Livepipeline (in dieser Reihenfolge) zu deaktivieren und zu löschen.

### <a name="deactivate-the-live-pipeline"></a>Deaktivieren der Livepipeline

Rufen Sie die direkte Methode `livePipelineDeactivate` mit der folgenden Nutzlast auf:

```json
{
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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
  "@apiVersion": "1.1",
  "name": "mdpipeline1"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

```json
{
  "status": 200,
  "payload": null
}
```

Der Statuscode „200“ gibt an, dass die Livepipeline erfolgreich gelöscht wurde.

Wenn Sie auch die Pipeline _mdpipeline2_ erstellt haben, können Sie die Pipelinetopologie nicht löschen, ohne auch diese zusätzliche Pipeline zu löschen. Rufen Sie die direkte Methode `livePipelineDelete` erneut auf, indem Sie die folgende Nutzlast verwenden:

```
{
    "@apiVersion" : "1.1",
    "name" : "mdpipeline2"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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
  "@apiVersion": "1.1",
  "name": "MotionDetection"
}
```

Nach einigen Sekunden wird im Fenster **AUSGABE** die folgende Antwort angezeigt:

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

- Probieren Sie die [Schnellstartanleitung zum Aufzeichnen von Videos in der Cloud bei Erkennung von Bewegungen](detect-motion-record-video-clips-cloud.md) aus.
- Probieren Sie die [Schnellstartanleitung zum Analysieren von Livevideos](analyze-live-video-use-your-model-http.md) aus.
- Informieren Sie sich ausführlicher über [Diagnosemeldungen](monitor-log-edge.md).
