---
title: Analysieren von Livevideos ohne Aufzeichnung - Azure
description: Eine Pipelinetopologie kann auch nur verwendet werden, um Analysen aus einem Livevideostream zu extrahieren, ohne dass dieser am Edge oder in der Cloud aufgezeichnet werden muss. Dieses Konzept wird in diesem Artikel erläutert.
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6230c565e7f193264c0640a3a278b2812f20ad7d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131847069"
---
# <a name="analyzing-live-videos-without-recording"></a>Analysieren von Livevideos ohne Aufzeichnung


## <a name="suggested-pre-reading"></a>Empfohlene Lektüre zur Vorbereitung 

* [Pipelinekonzept](pipeline.md)
* [Pipeline-Erweiterungskonzept](pipeline-extension.md)
* [Konzept für ereignisbasierte Videoaufzeichnung](event-based-video-recording-concept.md)

## <a name="overview"></a>Übersicht  

Sie können eine Pipelinetopologie verwenden, um Livevideos zu analysieren, ohne Teile des Videos in einer Datei oder einem Medienobjekt aufzuzeichnen. Die unten dargestellten Pipelinetopologien ähneln denen im Artikel zur [ereignisbasierten Videoaufzeichnung](event-based-video-recording-concept.md), aber ohne einen Senkenknoten für Videos oder Dateien.

> [!NOTE]
> Die Analyse von Live-Videos ist derzeit nur für das Edge-Modul und nicht für die Cloud verfügbar.

### <a name="motion-detection"></a>Bewegungserkennung

Die unten gezeigte Pipelinetopologie besteht aus einem [RTSP-Quellen](pipeline.md#rtsp-source)-Knoten, einem Knoten für den [Bewegungserkennungsprozessor](pipeline.md#motion-detection-processor) und einem Knoten für die [IoT Hub-Nachrichtensenke](pipeline.md#iot-hub-message-sink) - Sie können die Einstellungen sehen, die in der [JSON-Darstellung](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/motion-detection/topology.json) verwendet werden. Diese Topologie ermöglicht Ihnen das Erkennen von Bewegung im eingehenden Livevideostream und das Weiterleiten der Bewegungsereignisse an andere Apps und Dienste über den Senkenknoten für IoT Hub-Meldungen. Die externen Apps oder Dienste können eine Warnung auslösen oder eine Benachrichtigung an die entsprechenden Personen senden.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detection.svg" alt-text="Erkennen von Bewegungen in Livevideos":::

### <a name="analyzing-video-using-a-custom-vision-model"></a>Analysieren von Videos mit einem benutzerdefinierten Custom Vision-Modell 

Die unten gezeigte Pipelinetopologie ermöglicht Ihnen, einen Livevideostream mit einem benutzerdefinierten Custom Vision-Modell zu analysieren, das in einem separaten Modul gepackt ist. Sie können die Einstellungen sehen, die in der [JSON-Darstellung verwendet werden](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json). Es gibt weitere [Beispiele](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions) zum Umschließen von Modellen in IoT Edge, die als Rückschlussdienst ausgeführt werden.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/motion-detected-frames.svg" alt-text="Analysieren von Videos mit einem benutzerdefinierten Custom Vision-Modell":::

In dieser Pipelinetopologie wird die Videoeingabe von der RTSP-Quelle an einen [HTTP-Erweiterungsprozessorknoten](pipeline.md#http-extension-processor) gesendet, der Einzelbilder (in den Formaten JPEG, BMP oder PNG) über REST an einen externen Rückschlussdienst sendet. Die Ergebnisse des externen Rückschlussdiensts werden vom HTTP-Erweiterungsknoten abgerufen und über den Senkenknoten für IoT Hub-Meldungen an den IoT Edge-Hub weitergeleitet. Diese Art von Pipelinetopologie können Sie verwenden, um Lösungen für eine Vielzahl von Szenarien zu erstellen, z. B. zum Analysieren der Zeitreihenverteilung von Fahrzeugen an einer Kreuzung, von Bewegungsmustern der Kunden in einem Einzelhandelsgeschäft usw.

>[!TIP]
> Sie können die Bildfrequenz im HTTP-Erweiterungsprozessorknoten mithilfe des Felds `samplingOptions` verwalten, bevor Sie es downstream senden.

Als Erweiterung dieses Beispiels können Sie vor dem HTTP-Erweiterungsprozessorknoten einen Verarbeitungsknoten zur Bewegungserkennung verwenden. Damit reduzieren Sie die Auslastung des Rückschlussdiensts, da er nur verwendet wird, wenn im Video Bewegungsaktivitäten enthalten sind.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video-without-recording/custom-model.svg" alt-text="Analysieren von Livevideos mithilfe eines Custom Vision-Moduls für Frames mit Bewegung":::

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart: Analysieren von Livevideos mit einem eigenen Modell - HTTP](analyze-live-video-use-your-model-http.md)
