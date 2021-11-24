---
title: Überwachung und Protokollierung – Azure Video Analyzer-Dienst
description: Dieser Artikel bietet eine Übersicht der Überwachung und Protokollierung im Azure Video Analyzer-Dienst.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4a2a6b37c02aad5779085514bc5854df4d8a823
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400540"
---
# <a name="monitor-and-log"></a>Überwachen und Protokollieren

![Cloud-Symbol](media/env-icon/cloud.png)  
Alternativ können Sie den Monitor [auschecken und sich am Edge anmelden.](../edge/monitor-log-edge.md)

---

In diesem Artikel erfahren Sie mehr über Ereignisse und Protokolle, die vom Azure Video Analyzer-Dienst generiert werden. Außerdem erfahren Sie, wie Sie die vom Dienst generierten Protokolle nutzen und die Dienstereignisse überwachen.

## <a name="taxonomy-of-events"></a>Taxonomie von Ereignissen

Das folgende Diagramm zeigt die allgemeine Taxonomie, die für die Ereignisse oder Telemetriedaten verwendet wird, die vom Video Analyzer-Dienst ausgegeben werden:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-taxonomy-cloud.png" alt-text="Diagramm, das die Taxonomie von Ereignissen zeigt.":::

## <a name="event-schema"></a>Ereignisschema

 Vom Video Analyzer-Dienst generierte Ereignisse bestehen aus Systemeigenschaften, Anwendungseigenschaften und einem Text.

### <a name="common-properties"></a>Allgemeine Eigenschaften

Jedes Ereignis weist einen Satz allgemeiner Eigenschaften auf:

| Eigenschaft      | Eigenschaftstyp       | Datentyp | Beschreibung                                                  |
| ------------- | ------------------- | --------- | ------------------------------------------------------------ |
| `trace-id`    | System              | guid      | Eindeutige Ereignis-ID.                                             |
| `resourceId`  | applicationProperty | Zeichenfolge    | ARM-Pfad für das Azure Video Analyzer-Konto.               |
| `subject`     | applicationProperty | Zeichenfolge    | Unterpfad der Entität, die das Ereignis ausgibt.                    |
| `time`        | applicationProperty | Zeichenfolge    | Die Uhrzeit, zu der das Ereignis generiert wurde.                                |
| `category`    | System              | Zeichenfolge    | Überwachung, Betrieb, Diagnose.                              |
| `operationName`| applicationProperty| Zeichenfolge    | Ereignistypbezeichner (siehe folgenden Abschnitt).          |
| `level`        | System             | Zeichenfolge    | Ereignisstufe (Information, Warnung, Fehler, Kritisch).           |
| `body`        | body                | Objekt (object)    | Bestimmte Ereignisdaten.                                       |
| `operationVersion` | System         | Zeichenfolge    | Ereignisdatenversion {Major}. {Minor}                           |

## <a name="event-types"></a>Ereignistypen
Der Video Analyzer-Dienst gibt die folgenden Arten von Ereignisdaten aus:

**Betrieb:** Ereignisse, die von den Aktionen eines Benutzers oder während der Ausführung einer [Pipeline](../pipeline.md) generiert werden.
* Volumen: Es wird ein geringes Volumen erwartet (einige Male pro Minute oder sogar seltener). Beispiele:

| Ereignis        | Ebene               | Kurze Beschreibung                                              |
| ------------- | ------------------- |  ------------------------------------------------------------ |
|RecordingStarted   |Informational| Die Medienaufzeichnung hat begonnen|
|RecordingAvailable |Informational| Die Medienaufzeichnung ist verfügbar|
|RecordingStopped   |Informational| Die Medienaufzeichnung wurde beendet|
|PipelineStateChanged   |Informational| Der Status der Pipeline hat sich geändert|

   *Beispiel für ein Betriebsereignis*
      
```json
{
  "time": "2021-10-06T21:19:36.0988630Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Operational",
  "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
  "operationVersion": "1.0",
  "level": "Informational",
  "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
  "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipeline/sinks/videoSink",
    "body": {
      "type": "video",
      "location": "/videos/livetest1",
      "startTime": "2021-10-06T21:19:32.520Z"
    }
  }
}
```

**Diagnose:** Ereignisse, die bei der Diagnose von Problemen und/oder Leistungsproblemen helfen.
   * Volumen: Kann hoch sein (mehrmals pro Minute), dies kann sich auch auf die Speicherkosten auswirken. Es wird empfohlen, diese Ereignisse nur zu aktivieren, wenn eine Diagnose erforderlich ist, oder zu Problembehandlungszwecken. Beispiele:
    
| Ereignis             | Ebene           | Kurze Beschreibung                                            |
| ----------------- | ----------------| ------------------------------------------------------------ |
|AuthenticationError|Fehler|Fehler bei der Server-/Clientauthentifizierung|
|AuthorizationError |   Fehler|  Fehler bei der Server-/Clientautorisierung|
|FormatError        |   Fehler|  Probleme beim Verpacken, Formatieren oder Codieren|
|MediaSessionEstablished|   Informational|  SDP oder andere Sitzungsinformationen|
|NetworkError        |  Fehler|  DNS, Netzwerkfehler|
|ProtocolError        | Fehler|  RTSP oder ein anderer Protokollfehler|
|StorageError        |  Fehler|  Lese-/Schreibfehler beim Speichern|
|RtspPlaybackSessionEstablished|    Informational| Eine RTSP-Wiedergabesitzung wurde eingerichtet|
|RtspPlaybackSessionClosed| Informational|  Die RTSP-Wiedergabesitzung ist geschlossen|

*Beispiel für ein Diagnoseereignis*
  
```json
{
  "time": "2021-10-06T21:19:34.1290000Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Diagnostics",
  "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
  "operationVersion": "1.0",
  "level": "Informational",
  "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
  "properties": {
    "subject": "/livePipelines/livepipesample/sources/rtspSource",
    "body": {
        "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n"
    }
  }
}

```
**Überwachung:** Das Ereignis wird verwendet, um den API-Zugriff zu protokollieren. 
  * Volumen: Gering. Es wird empfohlen, diese Ereignisse nur zu aktivieren, wenn eine Überwachung erforderlich ist.

*Beispiel-Überwachungsprotokoll*
  
```json
{
  "time": "2021-10-07T23:53:31.6792370Z",
  "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
  "region": "westcentralus",
  "category": "Audit",
  "operationName": "Microsoft.VideoAnalyzer.Audit.ResourceGet",
  "level": "Warning",
  "uri": "https://{GUID}.api.{region}.videoanalyzer.azure.net/videos/batchjobsinknode",
  "resultType": "Failed",
  "resultSignature": "403",
  "identity": [
    {
      "alg": "RS256",
      "kid": "{KID}",
      "typ": "JWT"
    },
    {
      "sub": "livetest1",
      "aud": [ "https://{GUID}.streaming.{region}.videoanalyzer.azure.net/{GUID}", "wss://{GUID}.rtsp-tunnel.{region}.videoanalyzer.azure.net/{GUID}" ],
      "exp": 1633410145,
      "iss": "https://{region}.videoanalyzer.azure.net/"
    }
  ],
  "traceContext": "{\n  \"traceId\": \"4bb0dcf5-5c6d-4aa3-8c03-3f3d7e2c6210\"\n}",
  "properties": { "subject": "/videos/batchjobsinknodesample" }
}

```
## <a name="metrics"></a>Metriken

Der Video Analyzer-Dienst generiert Metriken für [Pipelines](../pipeline.md). Sie können auf die Metriken über das Azure-Portal zugreifen, indem Sie im Verwaltungsbereich Ihres Video Analyzer-Kontos zum Abschnitt „Überwachung“ navigieren.

| Metrikname                           | type    | Dimension                                                                              | Beschreibung                                                  |
| ------------------------------------- | ------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| Eingehende Bytes              | Leistungsindikator   | Art der Pipeline, Zustand, Name der Topologie                                 | Die Gesamtzahl der von einer Pipeline empfangenen Bytes. Wird nur für RTSP-Quellen unterstützt.           |
| Pipelines                  | Leistungsindikator   | Art der Pipeline, Zustand, Name der Topologie                        | Stellt die Pipelines in unterschiedlichen Zuständen zur Verfügung. Wird in regelmäßigen Abständen ausgegeben. |

## <a name="monitoring-of-events"></a>Überwachen von Ereignissen

Sie können die vom Video Analyzer-Dienst generierten Ereignisse in Ihrem Speicherkonto speichern und mithilfe von Azure Monitor nutzen.

**Azure Monitor-Ereignissammlung**

Der Video Analyzer-Dienst unterstützt derzeit das Schreiben von Telemetrieereignissen in ein Speicherkonto und die Verwendung von Azure Monitor zum Nutzen dieser Ereignisse. Verwenden Sie [Azure Monitor](../../../azure-monitor/overview.md), um vom Dienst generierte Ereignisse zu nutzen. Kunden erhalten über Azure Monitor eine integrierte Überwachungserfahrung.

Führen Sie die Schritte zum Aktivieren der Überwachung und Ereignissammlung mit Azure Monitor aus:
* Navigieren Sie im Azure-Portal zum Abschnitt **Überwachung** Ihres Video Analyzer-Kontos, und wählen Sie **Diagnoseeinstellungen** aus.
* Klicken Sie auf **Diagnoseeinstellung hinzufügen**, um die Erfassung der folgenden Protokolle zu konfigurieren:
    *   Bei Betrieb
    *   Diagnose
    *   Audit
* Wählen Sie die Protokollkategorie aus, die Sie aktivieren möchten, und den entsprechenden Aufbewahrungszeitraum.
* Wählen Sie für Zieldetails die Option **In einem Speicherkonto archivieren** aus, und geben Sie das Speicherkonto an, in dem diese Ereignisprotokolle gespeichert werden sollen.
    > [!IMPORTANT]
    > Der Video Analyzer-Dienst unterstützt derzeit nicht das Senden von Diagnosedaten an andere Ziele als Azure Storage.

* Klicken Sie nach dem Konfigurieren der Diagnoseeinstellungen auf **Speichern**.
* Navigieren Sie zum Zugreifen auf die Diagnoseprotokolle zum Speicher-Explorer, klappen Sie dann Ihr Speicherkonto auf, und Blobcontainer werden angezeigt. Der Container „insights-logs-category“ enthält Protokolle im JSON-Dateiformat. 
* Laden Sie die gewünschte Protokolldatei herunter. Der Inhalt der heruntergeladenen Protokolldatei sieht in etwa wie im folgenden Beispiel aus:

```json
{
    "time": "2021-10-06T21:19:36.0988630Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Operational",
    "operationName": "Microsoft.VideoAnalyzer.Operational.RecordingStarted",
    "operationVersion": "1.0",
    "level": "Informational",
    "correlationId": "c7887efd-0043-4ada-aa3d-9a411e612497",
    "traceContext": "{\n  \"traceId\": \"e74a9d4c-c4b9-4024-9acf-06be76d978ad\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipesample/sinks/videoSink",
        "body": {
            "type": "video",
            "location": "/videos/livetest1",
            "startTime": "2021-10-06T21:19:32.520Z"
        }
    }
}
{
    "time": "2021-10-06T21:19:34.1290000Z",
    "resourceId": "/SUBSCRIPTIONS/{SUBID}/RESOURCEGROUPS/{RGNAME}/PROVIDERS/MICROSOFT.MEDIA/VIDEOANALYZERS/{NAME}",
    "region": "westcentralus",
    "category": "Diagnostics",
    "operationName": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "operationVersion": "1.0",
    "level": "Informational",
    "traceContext": "{\n  \"traceId\": \"b03cabe2-b9d1-4be7-9770-4ac9e4fdc012\"\n}",
    "properties": {
        "subject": "/livePipelines/livepipe/sources/rtspSource",
        "body": {
            "sdp": "SDP:\nv=0\r\no=- 0 0 IN IP4 127.0.0.1\r\ns=No Name\r\nt=0 0\r\na=tool:libavformat 58.76.100\r\nm=video 0 RTP/AVP 96\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1; sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX; profile-level-id=4D0029\r\na=control:streamid=0\r\n" 
        }
    }
```

Ein **Aktivitätsprotokoll** für den Pipelinebetrieb wird ebenfalls automatisch generiert; der Zugriff erfolgt über den Abschnitt „Aktivitätsprotokoll“ des Video Analyzer-Kontos im Azure-Portal. Sie können den Verlauf der ARM-API-Aufrufe an Ihr Konto sowie relevante Details anzeigen.

![Beispiel für ein Aktivitätsprotokoll](./media/activity-log.png)


## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung für den Azure Video Analyzer-Dienst](./troubleshoot.md)
