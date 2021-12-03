---
author: fvneerden
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: faneerde
ms.custom: ignite-fall-2021
ms.openlocfilehash: ac02502e1f4958ec0fa6e0a445d720e3c9a00351
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860651"
---
Wenn Sie die Livepipeline ausführen, durchlaufen die Ergebnisse vom Knoten des Bewegungserkennungsprozessors den Knoten der IoT Hub-Nachrichtensenke und werden an den IoT-Hub gesendet. Die im **Ausgabefenster** von Visual Studio Code angezeigten Nachrichten enthalten jeweils den Abschnitt **body** und den Abschnitt **applicationProperties**. Weitere Informationen finden Sie unter [Erstellen und Lesen von IoT Hub-Nachrichten](../../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

In den folgenden Nachrichten werden vom Azure Video Analyzer-Modul die Anwendungseigenschaften und der Inhalt des Texts definiert.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished-Ereignis

Wenn eine Livepipeline aktiviert wird, versucht der Knoten der RTSP-Quelle eine Verbindung mit dem RTSP-Server herzustellen, der im Container „rtspsim-live555“ ausgeführt wird. Wenn die Verbindungsherstellung erfolgreich ist, wird das folgende Ereignis ausgegeben.

```
[IoTHubMonitor] [10:51:34 AM] Message received from [avasample-iot-edge-device/avaedge]:
{
  "body": {
  {
    "sdp&quot;: &quot;SDP:\nv=0\r\no=- 1620204694595500 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.08.19\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "properties": {
    "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ava-sample-deployment/providers/Microsoft.Media/videoAnalyzers/avasample",
    "subject": "/edgeModules/avaedge/livePipelines/Sample-Pipeline-1/sources/rtspSource",
    "eventType": "Microsoft.VideoAnalyzer.Diagnostics.MediaSessionEstablished",
    "eventTime": "2021-05-06T00:58:58.602Z",
    "dataVersion": "1.0"
  },
  "systemProperties": {
    "iothub-connection-device-id": "avasample-iot-edge-device",
    "iothub-connection-module-id": "avaedge",
    "iothub-message-source": "Telemetry",
    "messageId": "459c3255-7c86-4ff5-a1e5-7ce3fcb07627",
    "contentType": "application/json",
    "contentEncoding": "utf-8"
  }
}
```

Für die obige Ausgabe gilt Folgendes:

- Die Nachricht ist ein Diagnoseereignis (**MediaSessionEstablished**). Hierdurch wird angegeben, dass vom RTSP-Quellknoten („subject“) eine Verbindung mit dem RTSP-Simulator hergestellt wurde und der Empfang eines (simulierten) Livefeeds begonnen hat.
- Der Abschnitt **sdp** enthält Daten zum Diagnoseereignis. In diesem Fall umfassen die Daten die Details zum [Session Description Protocol (SDP)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="motiondetection-event"></a>Ereignis „MotionDetection“

Wenn Bewegung erkannt wird, sendet das Video Analyzer-Modul ein Rückschlussereignis. **type** wird auf **motion** festgelegt, um anzugeben, dass es sich um ein Ergebnis des Bewegungserkennungsprozessors handelt.

Hier ist ein Beispiel für diese Meldung angegeben:

```json
{
  "body": {
    "timestamp": 145818422564951,
    "inferences": [
      {
        "type": "motion",
        "motion": {
          "box": {
            "l": 0.322176,
            "t": 0.574627,
            "w": 0.525,
            "h": 0.088889
          }
        }
      }
    ]
  },
  "properties": { … },
  "systemProperties": { … }
}
```

In diesem Beispiel:

- Der Wert **body** enthält Daten zum Analyseereignis. In diesem Fall ist das Ereignis ein Rückschlussereignis. Daher enthält „body“ Daten für **timestamp** und **inferences**.
- Die Daten für **inferences** geben an, dass **type** auf **motion** festgelegt ist. Hier sind auch zusätzliche Daten zum Ereignis **motion** zu finden.
- Der Abschnitt **box** enthält die Koordinaten für einen Begrenzungsrahmen um das sich bewegende Objekt. Die Werte werden anhand der Breite und Höhe des Videos (in Pixel) normalisiert. Um beispielsweise die ursprünglichen Pixelkoordinaten abzurufen, multiplizieren Sie die horizontalen Dimensionen mit 1920 und die vertikalen Dimensionen mit 1080.

  ```
  l - distance from left of image
  t - distance from top of image
  w - width of bounding box
  h - height of bounding box
  ```
