---
title: 'Überwachung und Protokollierung: Azure'
description: Dieser Artikel bietet eine Übersicht der Überwachung und Protokollierung in Azure Video Analyzer.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed86a5de4fd5d65ec3e5a3b8112cb96c95f01811
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399937"
---
# <a name="monitor-and-log-on-iot-edge"></a>Überwachen und Protokollieren mit IoT Edge

![Edgesymbol](media/env-icon/edge.png)  
Alternativ können Sie den Monitor [auschecken und sich beim Dienst anmelden.](../cloud/monitor-log-cloud.md)

---

In diesem Artikel erfahren Sie, wie Sie Ereignisse vom Azure Video Analyzer IoT Edge-Modul für die Remoteüberwachung empfangen können. 

Darüber hinaus erfahren Sie, wie Sie die Protokolle steuern können, die vom Modul generiert werden.

## <a name="taxonomy-of-events"></a>Taxonomie von Ereignissen

Video Analyzer Auf Basis von IoT Edge gibt Ereignisse oder Telemetriedaten entsprechend der folgenden Taxonomie aus:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/telemetry-schema/taxonomy.png" alt-text="Diagramm, das die Taxonomie von Ereignissen zeigt.":::

* Betrieb: Ereignisse, die von den Aktionen eines Benutzers oder während der Ausführung einer [Pipeline](../pipeline.md) generiert werden
  
   * Volumen: Ein geringes Volumen wird erwartet (mehrmals pro Minute oder sogar seltener)
   * Beispiele:

      - Livepipeline aktiviert
      
      - Livepipeline deaktiviert
      
      *Beispiel für ein Betriebsereignis*
      
      ```json
      {
         "body": {
             "outputType": "filePath",
             "outputLocation": "/var/media/Sample-1-fileSink/sampleFilesFromEVR-motion-fileSinkOutput-20210426T181911Z.mp4"
           },
           "properties": {
             "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
             "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sinks/fileSink",
             "eventType": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
             "eventTime": "2021-04-26T18:19:13.298Z",
             "dataVersion": "1.0"
          },
      }
      ```
* Diagnose: Ereignisse, die bei der Diagnose von Leistungsproblemen helfen

   * Volumen: kann hoch sein (mehrmals pro Minute)
   * Beispiele:
   
      - RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol)-Informationen (wie im folgenden Beispiel gezeigt)       
      - Fehler, wenn z. B. keine Verbindung zu einer Kamera oder KI-Erweiterung hergestellt werden kann
      
    *Beispiel für ein Diagnoseereignis*
  
    ```json
    {
      "body": {
        "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
      },
      "applicationProperties": {
        "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
        "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
        "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
        "eventTime": "2021-04-26T18:15:13.298Z",
        "dataVersion": "1.0"
      }
    }
    ```
* Analyse: Ereignisse, die als Ergebnis einer Videoanalyse generiert wurden

   * Volumen: kann hoch sein (mehrmals pro Minute oder mehr)
   * Beispiele:
     
      - Bewegung erkannt (wie im folgenden Beispiel gezeigt) 
      
      - Rückschlussergebnis
     
      *Beispiel für ein Analyseereignis*
        
      ```json
      {
        "body": {
          "timestamp": 143039375044290,
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
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/Microsoft.Media/videoAnalyzers/<account-name>",
          "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/processors/md",
          "eventType": "Microsoft.VideoAnalyzer.Analytics.Inference",
          "eventTime": "2021-04-26T18:15:13.298Z",
          "dataVersion": "1.0"
        }
      }
      ```
        
Die vom Modul ausgegebenen Ereignisse werden an den [IoT Edge-Hub](../../../iot-edge/iot-edge-runtime.md#iot-edge-hub) gesendet. Von dort aus können sie an andere Ziele weitergeleitet werden. 

### <a name="events-and-video-playback"></a>Ereignisse und Videowiedergabe

Wie oben gezeigt, sind Ereignisse, die im Rahmen der Videoanalyse generiert werden, mit einem `eventTime` versehen. Wenn die [Livevideo-Aufzeichnung](../video-recording.md) im Rahmen der Pipelinetopologie erfolgt, können Sie damit ermitteln, wo das jeweilige Ereignis im aufgezeichneten Video zu finden ist. Sie können die Videoaufzeichnung im [Video Analyzer-Playerwidget](../player-widget.md) laden und dessen Steuerelemente verwenden, um das Datum und die Uhrzeit des betreffenden Ereignisses zu suchen. Wenn in der Pipeline ein KI-Modell zum Generieren von Rückschlussergebnissen verwendet wurde, sollten Sie die Rückschlussdaten zusammen mit dem Video aufzeichnen. Dadurch können Sie die Rückschlussmetadaten, wie in diesem [Tutorial](record-stream-inference-data-with-video.md) gezeigt, zusammen mit dem Video wiedergeben.


## <a name="routing-events"></a>Weiterleiten von Ereignissen

Mit den folgenden Eigenschaften von Modulzwillingen können Sie die Betriebs- und Diagnoseereignisse weiterleiten, die vom Video Analyzer-Modul veröffentlicht werden. Diese Eigenschaften sind im [JSON-Schema für Modulzwillinge](module-twin-configuration-schema.md) dokumentiert.

- `diagnosticsEventsOutputName`: Um Diagnoseereignisse vom Modul abzurufen, schließen Sie diese Eigenschaft ein, und stellen Sie einen beliebigen Wert für sie bereit. Lassen Sie die Eigenschaft aus oder leer, um das Modul an der Veröffentlichung von Diagnoseereignissen zu hindern.
  
- `operationalEventsOutputName`: Um Betriebsereignisse vom Modul abzurufen, schließen Sie diese Eigenschaft ein, und stellen Sie einen beliebigen Wert für sie bereit. Lassen Sie die Eigenschaft aus oder leer, um das Modul an der Veröffentlichung von Betriebsereignissen zu hindern.

Diagnose- und Betriebsereignisse werden automatisch von Knoten wie dem Bewegungserkennungsprozessor oder einem Erweiterungsprozessor ausgegeben. Analyseereignisse müssen in einer Pipeline an eine IoT Hub-Nachrichtensenke weitergeleitet werden, damit sie an den IoT Edge-Hub gesendet werden. 

Sie können das [Routing aller vorangehenden Ereignisse](../../../iot-edge/module-composition.md#declare-routes) über die Eigenschaft `desired` des `$edgeHub`-Modulzwillings im Bereitstellungsmanifest steuern:

```json
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.1",
     "routes": {
       "moduleToHub": "FROM /messages/modules/avaedge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

Im obigen JSON-Code ist `avaedge` der Name des Video Analyzer-Moduls. Die Routingregel folgt dem Schema, das in [Deklarieren von Routen](../../../iot-edge/module-composition.md#declare-routes) definiert ist.

> [!NOTE]
> Um sicherzustellen, dass die Analyseereignisse den IoT Edge-Hub erreichen, muss nach einem Knoten für die Bewegungserkennungsverarbeitung und/oder einem Knoten für die Erweiterungsverarbeitung ein Knoten vorhanden sein, der als IoT-Hub-Nachrichtensenke fungiert.

## <a name="event-schema"></a>Ereignisschema

Ereignisse, die vom Edge-Gerät stammen, können am Edge oder in der Cloud verarbeitet werden. Ereignisse, die von Video Analyzer generiert werden, entsprechen dem von Azure IoT Hub festgelegten [Streaming-Messagingmuster](../../../iot-hub/iot-hub-devguide-messages-construct.md). Das Muster besteht aus Systemeigenschaften, Anwendungseigenschaften und einem Text.

### <a name="summary"></a>Zusammenfassung

Wenn es über IoT Hub beobachtet wird, verfügt jedes Ereignis über eine Reihe allgemeiner Eigenschaften:

| Eigenschaft      | Eigenschaftstyp       | Datentyp | Beschreibung                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `message-id`  | System              | guid      | Eindeutige Ereignis-ID.                                             |
| `topic`       | applicationProperty | Zeichenfolge    | Azure Resource Manager-Pfad für das Azure Video Analyzer-Konto. |
| `subject`     | applicationProperty | Zeichenfolge    | Unterpfad der Entität, die das Ereignis ausgibt.                    |
| `eventTime`   | applicationProperty | Zeichenfolge    | Die Uhrzeit, zu der das Ereignis generiert wurde.                                |
| `eventType`   | applicationProperty | Zeichenfolge    | Ereignistypbezeichner. (Informationen hierzu finden Sie im nächsten Abschnitt.)          |
| `body`        | body                | Objekt (object)    | Bestimmte Ereignisdaten.                                       |
| `dataVersion` | applicationProperty | Zeichenfolge    | {Major}.{Minor}                                              |

### <a name="properties"></a>Eigenschaften

#### <a name="message-id"></a>message-id

Ein global eindeutiger Bezeichner (GUID, Globally Unique Identifier) für das Ereignis.

#### <a name="topic"></a>topic

Stellt das Video Analyzer-Edge-Modul dar, das das Ereignis generiert hat. Beispiel:

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/videoAnalyzers/{accountName}`

#### <a name="subject"></a>subject

Die Entität, die das Ereignis ausgibt. Beispiele:

`/edgeModules/avaedge/livePipelines/{livePipelineName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sources/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/processors/{nodeName}`<br/>
`/edgeModules/avaedge/livePipelines/{livePipelineName}/sinks/{nodeName}`

Mit der `subject`-Eigenschaft können Sie generische Ereignisse dem Knoten in der Livepipeline zuordnen, der das Ereignis ausgegeben hat. Beispielsweise würde im Fall eines ungültigen RTSP-Benutzernamens oder -Kennworts das Ereignis `Microsoft.VideoAnalyzer.Diagnostics.ProtocolError` vom `/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource`-Knoten generiert.

#### <a name="eventtype"></a>eventType

Ereignistypen werden einem Namespace gemäß diesem Schema zugewiesen:

`Microsoft.VideoAnalyzer.{EventClass}.{EventType}`

Die folgenden Ereignisklassen sind möglich:

| Klassenname  | Beschreibung                                                  |
| ----------- | ------------------------------------------------------------ |
| Analytics   | Als Teil der Inhaltsanalyse erstellte Ereignisse.                |
| Diagnose | Ereignisse, die bei der Diagnose von Problemen und Leistungsproblemen helfen. |
| Bei Betrieb | Im Rahmen des Ressourcenbetriebs generierte Ereignisse.              |

Beispiele:

* `Microsoft.VideoAnalyzer.Analytics.Inference`
* `Microsoft.VideoAnalyzer.Diagnostics.AuthorizationError`
* `Microsoft.VideoAnalyzer.Operational.RecordingStarted`

#### <a name="eventtime"></a>eventTime

Die Ereigniszeit wird in einer ISO 8601-Zeichenfolge formatiert. Sie gibt den Zeitpunkt an, zu dem das Ereignis eingetreten ist.

Beispiel:

`2021-04-26T18:15:13.298Z`

## <a name="metrics"></a>Metriken

Diese Metriken werden vom Video Analyzer-Modul über einen [Prometheus-Endpunkt](https://prometheus.io/docs/instrumenting/exposition_formats/#text-based-format) gemeldet, der auf dem Port 9600 des Moduls aktiv ist.

| Metrikname                           | type    | Bezeichnungen                                                                              | BESCHREIBUNG                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| va_active_live_pipelines              | Maßstab   | iothub, edge_device, module_name, pipeline_topology                                 | Gesamtanzahl der aktiven Livepipelines pro Topologie.          |
| va_cumulative_latency_seconds_average | Maßstab   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Die durchschnittliche Wartezeit vom Empfang eines Videoframes von einer Quelle bis zum endgültigen Verlassen des Knotens. Dies wird nicht gemeldet, wenn keine Frames vorhanden waren. |
| va_cumulative_latency_seconds_max     | Maßstab   | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Die maximale Wartezeit zwischen vom Empfang eines Videoframes von einer Quelle bis zum endgültigen Verlassen des Knotens. Dies wird nicht gemeldet, wenn keine Frames vorhanden waren. |
| va_data_dropped_total                 | Leistungsindikator | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node, data_kind | Zähler für alle gelöschten Daten (Ereignisse, Medien usw.).      |
| va_received_bytes_total               | Leistungsindikator | iothub, edge_device, module_name, pipeline_topology, live_pipeline, node            | Gesamtanzahl der von einem Knoten empfangenen Bytes. Wird nur für RTSP-Quellen unterstützt. |


> [!NOTE]
> Wenn Sie dem Video Analyzer-Modul den Namen `avaedge` zuweisen, ist durch Senden einer GET-Anforderung an `http://avaedge:9600/metrics` ein Zugriff auf die Metriken möglich. Je nach Bereitstellung müssen Sie diesen Port möglicherweise dem Gerät zuordnen, damit es erreichbar ist.

### <a name="azure-monitor-collection-via-telegraf"></a>Azure Monitor-Sammlung mithilfe von Telegraf 

Führen Sie die folgenden Schritte aus, um die Sammlung von Metriken des Video Analyzer-Moduls zu aktivieren. Die Metriken werden von [Telegraf](https://github.com/influxdata/telegraf) erfasst und dann als [benutzerdefinierte Metrik](../../../azure-monitor/essentials/metrics-custom-overview.md) in [Azure Monitor](../../../azure-monitor/overview.md) hochgeladen. In Telegraf werden die Protokolle vom [Prometheus](https://github.com/influxdata/telegraf/tree/master/plugins/inputs/prometheus)-Plug-In extrahiert und vom [azure_monitor](https://github.com/influxdata/telegraf/tree/master/plugins/outputs/azure_monitor)-Plug-In hochgeladen.

Der Agent kann verwendet zum Erfassen von Metriken von jedem Modul verwendet werden, das einen Prometheus-Endpunkt verfügbar macht.

1. Erstellen Sie auf dem Entwicklungscomputer einen Ordner, und navigieren Sie zu diesem Ordner.
1. Erstellen Sie in dem Ordner eine `telegraf.toml`-Datei, die folgende Konfigurationen enthält:
    ```toml
    [agent]
        interval = "30s"
        omit_hostname = true

    [[inputs.prometheus]]
      metric_version = 2
      urls = ["http://edgeHub:9600/metrics", "http://edgeAgent:9600/metrics", "http://{AVA_EDGE_MODULE_NAME}:9600/metrics"]

    [[outputs.azure_monitor]]
      namespace_prefix = "{AVA_EDGE_MODULE_NAME}"
      region = "westus2"
      resource_id = "/subscriptions/{SUBSCRIPTON_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Devices/IotHubs/{IOT_HUB_NAME}"
    ```
    > [!IMPORTANT]
    > Ersetzen Sie unbedingt die Variablen in der TOML-Datei. Die Variablen werden mit geschweiften Klammern (`{}`) gekennzeichnet. Aktualisieren Sie außerdem den Wert von `region`.
1. Erstellen Sie in demselben Ordner eine Dockerfile, die folgende Befehle enthält:
    ```docker
    FROM telegraf:1.15.3-alpine
    COPY telegraf.toml /etc/telegraf/telegraf.conf
    ```
1. Erstellen Sie mit Docker CLI-Befehlen die Docker-Datei, und veröffentlichen Sie das Image in Ihrer Azure Container Registry-Instanz.
   
   Weitere Informationen zur Verwendung der Docker CLI zum Pushen in eine Containerregistrierung finden Sie unter [Pushübertragung des ersten Images an eine private Containerregistrierung mit der Docker CLI](../../../container-registry/container-registry-get-started-docker-cli.md). Weitere Informationen zu Azure Container Registry finden Sie in der entsprechenden [Dokumentation](../../../container-registry/index.yml).

   ```bash
   # Insert your own container image URL
   docker build . -t myregistry.azurecr.io/telegraf_agent
   docker push myregistry.azurecr.io/telegraf_agent
   ```

1. Fügen Sie nach Abschluss des Pushens in Azure Container Registry den folgenden Knoten ihrer Bereitstellungsmanifestdatei hinzu:
    ```json
    "telegraf": 
    {
      "settings": 
        {
            "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_TELEGRAF_IMAGE}"
        },
        "type": "docker",
        "version": "1.0",
        "status": "running",
        "restartPolicy": "always",
        "env": 
        {
            "AZURE_TENANT_ID": { "value": "{YOUR_TENANT_ID}" },
            "AZURE_CLIENT_ID": { "value": "{YOUR CLIENT_ID}" },
            "AZURE_CLIENT_SECRET": { "value": "{YOUR_CLIENT_SECRET}" }
        }
    }
    ```
    > [!IMPORTANT]
    > Ersetzen Sie unbedingt die Variablen in der Manifestdatei. Die Variablen werden mit geschweiften Klammern (`{}`) gekennzeichnet.


   Azure Monitor kann [über den Dienstprinzipal authentifiziert werden](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication).
        
   Das Azure Monitor-Telegraf-Plug-In stellt [verschiedene Methoden der Authentifizierung](https://github.com/influxdata/telegraf/blob/master/plugins/outputs/azure_monitor/README.md#azure-authentication) bereit. 
  1. Um die Dienstprinzipalauthentifizierung zu verwenden, legen Sie diese Umgebungsvariablen fest:  
     `AZURE_TENANT_ID`: Gibt den Mandanten für die Authentifizierung an.  
     `AZURE_CLIENT_ID`: Gibt die zu verwendende App-Client-ID an.  
     `AZURE_CLIENT_SECRET`: Gibt das zu verwendende App-Geheimnis an.  
     
     >[!TIP]
     >Sie können dem Dienstprinzipal die Rolle **Herausgeber von Überwachungsmetriken** zuweisen. Führen Sie die Schritte in **[Erstellen eines Dienstprinzipals](../../../azure-arc/data/upload-metrics-and-logs-to-azure-monitor.md?pivots=client-operating-system-macos-and-linux#create-service-principal)** aus, um den Dienstprinzipal zu erstellen und die Rolle zuzuweisen.
1. Sobald die Module bereitgestellt sind, werden die Metriken in Azure Monitor unter einem einzigen Namespace angezeigt. Metriknamen entsprechen den von Prometheus ausgegebenen. 

   Wechseln Sie in diesem Fall im Azure-Portal zum IoT-Hub, und wählen Sie im linken Bereich **Metriken** aus. Die Metriken sollten dort angezeigt werden. 

### <a name="log-analytics-metrics-collection"></a>Metrikerfassung in Log Analytics

Wenn Sie den [Prometheus-Endpunkt](https://prometheus.io/docs/practices/naming/) zusammen mit [Log Analytics](../../../azure-monitor/logs/log-analytics-tutorial.md) verwenden, können Sie [Metriken generieren und überwachen](../../../azure-monitor/essentials/metrics-supported.md), z. B. Nutzungsdaten zu CPUPercent oder MemoryUsedPercent.   

> [!NOTE]
> Bei der folgenden Konfiguration werden keine Protokolle erfasst, **nur Metriken**. Es ist möglich, das Erfassungsmodul so zu erweitern, dass auch Protokolle erfasst und hochgeladen werden.

[![Abbildung der Metrikerfassung mithilfe von Log Analytics.](./media/telemetry-schema/log-analytics.svg)](./media/telemetry-schema/log-analytics.svg#lightbox)

1. Informieren Sie sich über die [Metrikerfassung](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector).
1. Erstellen Sie mit Docker-CLI-Befehlen die [Docker-Datei](https://github.com/Azure/iotedge/tree/master/edge-modules/MetricsCollector/docker/linux), und veröffentlichen Sie das Image in Ihrer Azure Container Registry-Instanz.
   
   Weitere Informationen zur Verwendung der Docker CLI zum Pushen in eine Containerregistrierung finden Sie unter [Pushübertragung des ersten Images an eine private Containerregistrierung mit der Docker CLI](../../../container-registry/container-registry-get-started-docker-cli.md). Weitere Informationen zu Azure Container Registry finden Sie in der [Dokumentation](../../../container-registry/index.yml).
1. Nach Abschluss des Pushvorgangs zu Azure Container Registry wird im Bereitstellungsmanifest Folgendes hinzugefügt:
    ```json
    "azmAgent": {
      "settings": {
        "image": "{AZURE_CONTAINER_REGISTRY_URL_OF_YOUR_METRICS_COLLECTOR}"
      },
      "type": "docker",
      "version": "1.0",
      "status": "running",
      "restartPolicy": "always",
      "env": {
        "LogAnalyticsWorkspaceId": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_ID}" },
        "LogAnalyticsSharedKey": { "value": "{YOUR_LOG_ANALYTICS_WORKSPACE_SECRET}" },
        "LogAnalyticsLogType": { "value": "IoTEdgeMetrics" },
        "MetricsEndpointsCSV": { "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics,http://avaedge:9600/metrics" },
        "ScrapeFrequencyInSecs": { "value": "30 " },
        "UploadTarget": { "value": "AzureLogAnalytics" }
      }
    }
    ```
    > [!NOTE]
    > Bei den Modulen `edgeHub`, `edgeAgent` und `avaedge` handelt es sich um die Namen der Module, die in der Bereitstellungsmanifestdatei definiert werden. Sorgen Sie dafür, dass die Modulnamen übereinstimmen.   

    Die Werte für `LogAnalyticsWorkspaceId` und `LogAnalyticsSharedKey` erhalten Sie, wenn Sie die folgenden Schritte ausführen:
    1. Wechseln Sie zum Azure-Portal.
    1. Suchen Sie nach Ihren Log Analytics-Arbeitsbereichen.
    1. Navigieren Sie zur `Agents management`-Option im linken Navigationsbereich, sobald Sie Ihren Log Analytics-Arbeitsbereich gefunden haben.
    1. Dort finden Sie die Arbeitsbereichs-ID und die geheimen Schlüssel, die Sie verwenden können.

1. Erstellen Sie als Nächstes eine Arbeitsmappe, indem Sie im linken Navigationsbereich auf die `Workbooks`-Registerkarte klicken.
1. Mithilfe der Kusto-Abfragesprache können Sie Abfragen wie die folgenden schreiben und den von den IoT Edge-Modulen verwendeten CPU-Prozentwert ermitteln.
    ```kusto
    let cpu_metrics = IoTEdgeMetrics_CL
    | where Name_s == "edgeAgent_used_cpu_percent"
    | extend dimensions = parse_json(Tags_s)
    | extend module_name = tostring(dimensions.module_name)
    | where module_name in ("avaedge","yolov3","tinyyolov3")
    | summarize cpu_percent = avg(Value_d) by bin(TimeGenerated, 5s), module_name;
    cpu_metrics
    | summarize cpu_percent = sum(cpu_percent) by TimeGenerated
    | extend module_name = "Total"
    | union cpu_metrics
    ```

    [ ![Diagramm, das die Metriken mithilfe der Kusto-Abfrage anzeigt.](./media/telemetry-schema/metrics.png)](./media/telemetry-schema/metrics.png#lightbox)
## <a name="logging"></a>Protokollierung

Wie bei anderen IoT Edge-Modulen, können Sie außerdem [die Containerprotokolle auf dem Edge-Gerät untersuchen](../../../iot-edge/troubleshoot.md#check-container-logs-for-issues). Sie können die in den Protokollen erfassten Informationen durch die [folgenden Modulzwillingseigenschaften](module-twin-configuration-schema.md) konfigurieren:

* `logLevel`

   * Zulässige Werte sind `Verbose`, `Information`, `Warning`, `Error` und `None`.
   * Der Standardwert ist `Information`. Die Protokolle enthalten Fehler, Warnungen und Informationen.
   * Wenn Sie den Wert auf `Warning` festlegen, enthalten die Protokolle Fehler- und Warnmeldungen.
   * Wenn Sie den Wert auf `Error` festlegen, enthalten die Protokolle nur Fehlermeldungen.
   * Wenn Sie den Wert auf `None` festlegen, werden keine Protokolle generiert. Diese Vorgehensweise wird nicht empfohlen.
   * Verwenden Sie `Verbose` nur, wenn Sie Protokolle für den Azure-Support freigeben müssen, um ein Problem zu diagnostizieren.

* `logCategories`

   * Eine durch Trennzeichen getrennte Liste einer oder mehrerer dieser Werte: `Application`, `Events`, `MediaPipeline`.
   * Standardwert: `Application, Events`.
   * `Application`: Allgemeine Informationen aus dem Modul wie Nachrichten zum Modulstart, Umgebungsfehler und Aufrufen direkter Methoden.
   * `Events`: Alle Ereignisse, die weiter oben in diesem Artikel beschrieben sind.
   * `MediaPipeline`: Detailprotokolle, die möglicherweise Einblicke bei der Problembehandlung geben können, etwa bei Problemen beim Herstellen einer Verbindung mit einer RTSP-fähigen Kamera.
   
### <a name="generating-debug-logs"></a>Generieren von Debugprotokollen
In bestimmten Fällen müssen Sie möglicherweise ausführlichere Protokolle als die oben beschriebenen generieren, um den Azure-Support beim Beheben eines Problems zu unterstützen. So generieren Sie diese Protokolle:  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub-Instanz.
1. Wählen Sie im linken Bereich die Option **IoT Edge** aus.
1. Wählen Sie in der Liste der Geräte die ID des Zielgeräts aus.
1. Wählen Sie oben im Bereich die Option **Module festlegen** aus.

   ![Screenshot der Schaltfläche „Module festlegen“ im Azure-Portal](media/troubleshoot/set-modules.png)

1. Suchen Sie im Abschnitt **IoT Edge Modules** nach **avaedge** und wählen Sie Letzteres aus.
1. Wählen Sie **Zwilling der Modulkennung** aus. Ein bearbeitbarer Bereich wird geöffnet.
1. Fügen Sie unter **desired key** das folgende Schlüssel-Wert-Paar hinzu:

   `"DebugLogsDirectory": "/var/lib/videoanalyzer/logs"`

   > [!NOTE]
   > Dieser Befehl bindet die Protokollordner zwischen dem Edge-Gerät und dem Container. Wenn Sie die Protokolle an einem anderen Speicherort auf dem Gerät sammeln möchten:
   >
   > 1. Erstellen Sie eine Bindung für den Speicherort des Debugprotokolls im Abschnitt **Binds**, wobei **$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE** und **$DEBUG_LOG_LOCATION** durch den von Ihnen gewünschten Speicherort ersetzt werden: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
   > 2. Verwenden Sie den folgenden Befehl, wobei **$DEBUG_LOG_LOCATION** durch den im vorherigen Schritt verwendeten Speicherort ersetzt wird: `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`

1. Wählen Sie **Speichern** aus.

Das Modul schreibt nun Debugprotokolle in einem binären Format in den Gerätespeicherpfad `/var/local/videoAnalyzer/debuglogs/`. Sie können diese Protokolle für den Azure-Support freigeben.  

Sie können die Protokollsammlung beenden, indem Sie den Wert unter **Zwilling der Modulkennung** auf _NULL_ festlegen. Wechseln Sie zurück zur Seite **Zwilling der Modulkennung**, und aktualisieren Sie den folgenden Parameter wie folgt:

   `"DebugLogsDirectory": ""`

## <a name="faq"></a>Häufig gestellte Fragen

Wenn Sie Fragen haben, finden Sie weitere Informationen in den [häufig gestellten Fragen zu Überwachung und Metriken](faq.yml#monitoring-and-metrics).

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung für Azure Video Analyzer](troubleshoot.md)
