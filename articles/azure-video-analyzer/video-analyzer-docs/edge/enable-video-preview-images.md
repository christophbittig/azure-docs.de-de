---
title: Aktivieren von Vorschaubildern
description: In diesem Artikel erfahren Sie, wie Sie Vorschaubilder aktivieren und darauf zugreifen, wenn Sie Videos mithilfe von Azure Video Analyzer aufzeichnen.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: dd78a3753f61fe5615f15a41d1b1493556d0dafc
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559426"
---
# <a name="enable-preview-images-when-recording-video"></a>Aktivieren von Vorschaubildern beim Aufzeichnen von Videos

Mit Azure Video Analyzer können Sie Videos von einer RTSP-Kamera [erfassen und aufzeichnen](../video-recording.md). Dazu muss eine Pipelinetopologie mit einem Videosenke-Knoten erstellt werden, wie in [dieser Schnellstartanleitung](detect-motion-record-video-clips-cloud.md) oder in [diesem Tutorial](use-continuous-video-recording.md) gezeigt. 

Wenn Sie Videos mit dem Video Analyzer-Edgemodul aufzeichnen, können Sie die regelmäßige Generierung einer Reihe von Vorschaubildern unterschiedlicher Größe durch den Videosenke-Knoten ermöglichen. Diese Bilder können dann von der [Videoressource](../terminology.md#video) in Ihrem Video Analyzer-Konto abgerufen werden. Wenn Ihre Kamera beispielsweise ein Video mit einer Auflösung von 1.920 × 1.080 generiert, haben die Vorschaubilder folgende Größen:

  * 320 × 180: Klein
  * 640 × 360: Mittel
  * 1\.280 × 720: Groß

> [!NOTE]
> Bei den Vorschaubildern wird das Seitenverhältnis des Kameravideos beibehalten.

Die Vorschaubilder werden in regelmäßigen Abständen generiert. Die Häufigkeit hängt von [`segmentLength`](../playback-recordings-how-to.md#recording-and-playback-latencies) ab. Beachten Sie bei Verwendung der [ereignisbasierten Aufzeichnung](record-event-based-live-video.md), dass Bilder nur generiert werden, wenn die Livepipeline aktiv ist und ein Video aufgezeichnet wird. Bei jeder Generierung von Vorschaubildern werden die vorherigen Vorschaubilder überschrieben.

> [!NOTE]
> Diese Funktion steht derzeit nur mit dem Video Analyzer-Edgemodul zur Verfügung. Darüber hinaus wirkt sich die Aktivierung dieser Funktion auf Ihre Azure-Speicherkosten aus. Grund sind die häufigen Transaktionen zum Schreiben oder Anzeigen der Bilder sowie die Größe der Bilder.

## <a name="enable-preview-images-in-the-video-sink-node"></a>Aktivieren von Vorschaubildern im Videosenke-Knoten
Wenn Sie Vorschaubilder aktivieren möchten, müssen Sie im Videosenke-Knoten der Pipelinetopologie das entsprechende Flag festlegen. Legen Sie unter **videoPublishingOptions** die Option **enableVideoPreviewImage** auf **true** fest.  

Beispiel:
```
        "sinks": [
        {
          "@type": "#Microsoft.VideoAnalyzer.VideoSink",
          "name": "videoSink",
          "videoName": "{$parameter-for-specifying-unique-videoName-for-each-pipeline}",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": [
                {
                  "property": "mediaType",
                  "operator": "is",
                  "value": "video"
                }
              ]
            }
          ],
          "videoPublishingOptions": {
            "enableVideoPreviewImage": true
          },
          "videoCreationProperties": {
            "title": "{$parameter-for-specifying-unique-title-for-each-pipeline}",
            "description": "{$parameter-for-specifying-unique-description-for-each-pipeline}k",
            "segmentLength": "PT30S"
          },
          "localMediaCachePath": "/var/lib/videoanalyzer/tmp/",
          "localMediaCacheMaximumSizeMiB": "2048"
        }
      ]
``` 

## <a name="accessing-preview-images"></a>Zugreifen auf Vorschaubilder

Um die statischen URLs für die verfügbaren Vorschaubilder abzurufen, muss für die Videoressource eine GET-Anforderung mit einem [autorisierten Bearertoken](../playback-recordings-how-to.md#accessing-videos) aufgerufen werden. Die URLs werden in der Antwort unter **contentUrls** aufgeführt, wie hier zu sehen:

```
      "contentUrls": {
        ...
        "previewImageUrls": {
          "small": "XXXX",
          "medium": "XXXX",
          "large": "XXXX"
         }
       },
    
```

## <a name="next-steps"></a>Nächste Schritte

In [dieser Schnellstartanleitung](detect-motion-record-video-clips-cloud.md) oder [diesem Tutorial](use-continuous-video-recording.md) können Sie das Aktivieren von Videovorschaubildern in der Topologie ausprobieren. 
