---
title: 'Ereignisbasierte Videoaufzeichnung mit Azure Video Analyzer: Azure'
description: Ereignisbasierte Videoaufzeichnung (EVR) bezeichnet den Vorgang des Aufzeichnen von Videos mit Auslösung durch ein Ereignis. Das betreffende Ereignis kann durch die Verarbeitung des Videosignals selbst (z. B. bei Erkennung einer Bewegung) oder von einer unabhängigen Quelle stammen (z. B. bei Signalisierung eines Türsensors, dass die Tür geöffnet wurde). In diesem Artikel werden einige Anwendungsfälle der ereignisbasierten Videoaufzeichnung beschrieben.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 721e86c43dc0268eaca3fb83b7b2784249f6010e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844561"
---
# <a name="event-based-video-recording"></a>Ereignisbasierte Videoaufzeichnung  

Ereignisbasierte Videoaufzeichnung (EVR) bezeichnet den Vorgang des Aufzeichnen von Videos mit Auslösung durch ein Ereignis. Das betreffende Ereignis kann durch die Verarbeitung des Videosignals selbst (z. B. bei Erkennung einer Bewegung) oder von einer unabhängigen Quelle stammen (z. B. bei Signalisierung eines Türsensors, dass die Tür geöffnet wurde). In diesem Artikel werden einige Anwendungsfälle der ereignisbasierten Videoaufzeichnung beschrieben.

Die Zeitstempel für die Aufzeichnungen werden in UTC gespeichert. Aufgezeichnete Videos können mithilfe der Streamingfunktionen des Video Analyzer wiedergespielt werden. Weitere Informationen finden Sie unter [Wiedergabe von Videoaufzeichnungen](playback-recordings-how-to.md).

## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung  

* [Pipelinekonzept](pipeline.md)
* [Videoaufzeichnung: Konzepte](video-recording.md) 

## <a name="overview"></a>Übersicht 

Video Analyzer bietet zwei Möglichkeiten, um eine ereignisbasierte Videoaufzeichnung durchzuführen:
* Zeichnen Sie die Eingabe von einer bestimmten RTSP-fähigen IP-Kamera in einer bestimmten Videoressource in der Cloud auf, wobei jedes neue Ereignis an die verfügbare Aufzeichnung in dieser Videoressource angefügt wird.
* Zeichnen Sie das Video in getrennten MP4-Dateien im lokalen Speicher des IoT Edge-Geräts auf, wobei jedes Ereignis zu einer neuen MP4-Datei führt.

In diesem Artikel werden einige Anwendungsfälle im Zusammenhang mit der ereignisbasierten Videoaufzeichnung beschrieben.

### <a name="video-recording-triggered-by-motion-detection"></a>Durch Bewegungserkennung ausgelöste Videoaufzeichnung  

In diesem Anwendungsfall können Sie Videoclips nur aufzeichnen, wenn im Video Bewegungen erkannt werden, und Sie können nur benachrichtigt werden, wenn ein Videoclip generiert wird. Dies könnte in kommerziellen Sicherheitsszenarios von Bedeutung sein, die den Schutz einer kritischen Infrastruktur betreffen. Die ereignisbasierte Videoaufzeichnung hilft Ihnen dabei, Speicherkosten zu verringern.

Die folgende Abbildung zeigt eine grafische Darstellung einer Pipeline für diesen Anwendungsfall. Die JSON-Darstellung der Pipelinetopologie einer solchen Pipeline finden Sie [hier](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-motion-video-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/motion-detection.svg" alt-text="Ereignisbasierte Aufzeichnung von Livevideos bei Erkennung einer Bewegung.":::

In der Abbildung zeichnet der Knoten der RTSP-Quelle den Livevideofeed von der Kamera auf und überträgt ihn an einen Knoten des [Bewegungserkennungsprozessors](pipeline.md#motion-detection-processor). Sobald im Livevideo eine Bewegung erkannt wird, generiert der Knoten des Bewegungserkennungsprozessors Ereignisse, die an den Knoten des [Signalgateprozessors](pipeline.md#signal-gate-processor) sowie an den Knoten der IoT Hub-Nachrichtensenke gesendet werden. Der letztere Knoten sendet die Ereignisse an den IoT Edge-Hub. Von dort aus können sie zum Auslösen von Warnungen an andere Ziele weitergeleitet werden. 

Ereignisse vom Knoten für die Bewegungserkennung triggern auch den Knoten des Signalgateprozessors, sodass dieser geöffnet und der Livevideofeed vom RTSP-Quellknoten an den [Knoten der Videosenke](pipeline.md#video-sink) übertragen wird. Wenn keine weiteren Bewegungserkennungsereignisse vorhanden sind, wird das Gate nach einem konfigurierten Zeitraum geschlossen. Dadurch wird die Dauer des aufgezeichneten Videos bestimmt, das an die Videoressource angefügt wird.

### <a name="video-recording-based-on-events-from-other-sources"></a>Videoaufzeichnung auf der Grundlage von Ereignissen aus anderen Quellen  

In diesem Anwendungsfall können Signale von einem anderen IoT-Sensor verwendet werden, um die Aufzeichnung von Videos auszulösen. Die folgende Abbildung zeigt eine grafische Darstellung einer Pipeline für diesen Anwendungsfall. Die JSON-Darstellung der Pipelinetopologie einer solchen Pipeline finden Sie [hier](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/other-sources.svg" alt-text="Ereignisbasierte Aufzeichnung von Livevideos bei Signalisierung von einer externen Quelle.":::

In der Abbildung sendet der externe Sensor Ereignisse an den IoT Edge-Hub. Die Ereignisse werden über den Knoten der [IoT Hub-Nachrichtenquelle](pipeline.md#iot-hub-message-source) an den Knoten des Signalgateprozessors weitergeleitet. Das Verhalten des Knotens des Signalgateprozessors ist mit dem vorherigen Anwendungsfall identisch: Bei Auslösung durch ein Ereignis wird der Knoten geöffnet und der Livevideofeed wird über den Knoten der RTSP-Quelle an den Knoten der Dateisenke übertragen. Jedes Mal, wenn das Gate geöffnet wird, wird eine neue MP4-Datei im lokalen Speicher des IoT Edge-Geräts geschrieben.

### <a name="video-recording-based-on-an-external-inferencing-module"></a>Videoaufzeichnung auf der Grundlage eines externen Rückschlussmoduls 

In diesem Anwendungsfall können Sie Videos auf der Grundlage eines Signals von einem externen logischen System aufzeichnen. Ein Beispiel für einen Anwendungsfall dieser Art ist die Aufzeichnung eines Videos in der Cloud, wenn im Videofeed des Verkehrs auf einer Schnellstraße ein LKW erkannt wird. Die folgende Abbildung zeigt eine grafische Darstellung einer Pipeline für diesen Anwendungsfall. Die JSON-Darstellung der Pipelinetopologie einer solchen Pipeline finden Sie [hier](https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-concept/external-inferencing-module.svg" alt-text="Ereignisbasierte Aufzeichnung von Livevideos bei Signalisierung von einem externen Rückschlussmodul.":::

In der Abbildung zeichnet der Knoten der RTSP-Quelle den Livevideofeed von der Kamera auf und überträgt ihn an zwei Branchen: einer enthält den Knoten des [Signalgateprozessors](pipeline.md#signal-gate-processor), und der andere verwendet einen Knoten der [HTTP-Erweiterung](pipeline.md#http-extension-processor), um Daten an ein externes Logikmodul zu senden. Mithilfe des Knotens der HTTP-Erweiterung kann die Pipeline Bildframes (im Format JPEG, BMP oder PNG) über REST an einen externen Rückschlussdienst senden. Dieser Signalpfad unterstützt in der Regel nur niedrige Frameraten (< 5 BpS). Mit dem Knoten des Prozessors für die HTTP-Erweiterung können Sie die Bildfrequenz des Videos reduzieren, das an das externe Rückschlussmodul gesendet wird.

Die Ergebnisse des externen Rückschlussdiensts werden vom Knoten der HTTP-Erweiterung abgerufen und über den Knoten der IoT Hub-Nachrichtensenke an den IoT Edge-Hub weitergeleitet. Dort können sie vom externen Logikmodul weiter verarbeitet werden. Wenn der Rückschlussdienst beispielsweise Fahrzeuge erkennen kann, kann das Logikmodul nach einem bestimmten Fahrzeug wie etwa nach einem LKW suchen. Wenn das Logikmodul das gesuchte Objekt erkennt, kann es den Knoten des Signalgateprozessors ansteuern, indem es ein Ereignis über den IoT Edge-Hub an den Knoten der IoT Hub-Nachrichtenquelle in der Pipeline sendet. Die Ausgabe des Signalgates wird einen Knoten übertragen, der als Videosenke fungiert. Bei jeder Erkennung eines LKWs wird ein Video in der Cloud aufgezeichnet (an die Videoressource angefügt).

Als Erweiterung dieses Beispiels können Sie vor dem HTTP-Erweiterungsprozessorknoten einen Verarbeitungsknoten zur Bewegungserkennung verwenden. Dadurch wird die Last für den Rückschlussdienst reduziert, z. B. nachts, wenn lange Zeit keine Fahrzeuge auf der Schnellstraße unterwegs sind.

## <a name="resiliency"></a>Resilienz
Weitere Informationen finden Sie [im Hinweis zur robusten Aufzeichnung](continuous-video-recording.md#resilient-recording), die auch für EVR gilt.

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Ereignisbasierte Videoaufzeichnung](record-event-based-live-video.md)
* [Wiedergeben aufgezeichneter Inhalte](playback-recordings-how-to.md)
