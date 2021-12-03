---
title: Verwenden der Visual Studio Code-Erweiterung für den Azure Video Analyzer
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der Visual Studio Code-Erweiterung für den Azure Video Analyzer erläutert.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0784f69e40597bef3a08512e3bcbd01cf1343390
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552561"
---
# <a name="use-the-visual-studio-code-extension-for-azure-video-analyzer"></a>Verwenden der Visual Studio Code-Erweiterung für den Azure Video Analyzer

[!INCLUDE [header](includes/edge-env.md)]

In dieser Schnellstartanleitung werden die ersten Schritte mit der Visual Studio Code-Erweiterung für den Azure Video Analyzer erläutert. Sie verbinden die Visual Studio Code-Erweiterung über den IoT Hub mit Ihrem Video Analyzer Edge-Modul und stellen eine [Beispielpipelinetopologie](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) bereit. Anschließend führen Sie einen simulierten Livevideostream über eine Livepipeline aus, die kontinuierlich Videodaten in einer Videoressource aufzeichnet. Das folgende Diagramm stellt die Pipeline dar.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/use-continuous-video-recording/continuous-video-recording-overview.svg" alt-text="Fortlaufende Videoaufzeichnung":::
 
 ## <a name="prerequisites"></a>Voraussetzungen
 
* [Visual Studio Code](https://code.visualstudio.com/), mit der [Azure Video Analyzer-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)
* Entweder ders [Tutorial Schnellstart: Erste Schritte mit dem Azure Video Analyzer](./get-started-detect-motion-emit-events.md) oder Tutorial zur [Kontinuierlichen Videoaufzeichnung und -wiedergabe](./use-continuous-video-recording.md)

 > [!NOTE]
 > Die Bilder in diesem Artikel basieren auf dem Tutorial [Kontinuierliche Videoaufzeichnung und -wiedergabe](./use-continuous-video-recording.md).    

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

### <a name="obtain-your-iot-hub-connection-string"></a>Abrufen der IoT Hub-Verbindungszeichenfolge

Zum Ausführen von Aufrufen des Video Analyzer Edge-Moduls wird zunächst eine Verbindungszeichenfolge benötigt, um die Visual Studio Code-Erweiterung mit dem IoT Hub zu verbinden.

1. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub-Konto.
1. Suchen Sie im linken Bereich nach **Richtlinien für gemeinsamen Zugriff**, und wählen Sie die entsprechende Option aus.
1. Wählen Sie die Richtlinie **iothubowner** aus.
1. Kopieren Sie den Wert unter **Primäre Verbindungszeichenfolge**. Er sieht wie folgt aus: `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX`.

### <a name="connect-the-visual-studio-code-extension-to-the-iot-hub"></a>Verbinden Sie die Visual Studio Code-Erweiterung an den IoT Hub

Verbinden Sie die Visual Studio Code-Erweiterung mithilfe Ihrer IoT Hub Verbindungszeichenfolge mit dem Video Analyzer-Modul. 

1.  Wählen Sie in Visual Studio Code ganz links in der Aktivitätsleiste das Symbol **Azure Video Analyzer** aus.
1.  Klicken Sie im Bereich der Video Analyzer-Erweiterung auf die Schaltfläche **Verbindungszeichenfolge eingeben**.
1.  Fügen Sie oben die IoT Hub Verbindungszeichenfolge ein.
1.  Wählen Sie das Gerät aus, auf dem AVA bereitgestellt wird. Die Standardeinstellung lautet `avasample-iot-edge-device`.
1.  Wählen Sie das Azure Video Analyzer-Modul aus. Die Standardeinstellung lautet `avaedge`.

![Gif, das zeigt, wie die Verbindungszeichenfolge eingegeben wird](./media/use-visual-studio-code-extension/enter-connection-string.gif)

Im Bereich "Video Analyzer-Erweiterung" sollte nun das verbundene Gerät mit allen zugehörigen Modulen angezeigt werden. Unterhalb der Module werden Pipelinetopologien aufgeführt. Standardmäßig sind keine Pipelinetopologien bereitgestellt.

## <a name="create-a-pipeline-topology"></a>Pipelinetopologie erstellen 

Mit einer [Pipeline-Topologie](../pipeline.md) können Sie beschreiben, wie Live-Videos oder aufgezeichnete Videos für Ihre individuellen Anforderungen über eine Reihe miteinander verbundener Knoten verarbeitet und analysiert werden sollen. 

1.  Klicken Sie links unter **Module** mit der rechten Maustaste auf **Pipelinetopologien** und wählen Sie die Option zum **Erstellen einer Pipelinetopologie** aus.
1.  Wählen Sie oben unter **Beispieltopologien ausprobieren** unter **Fortlaufende Videoaufzeichnung** die Option **In Azure Video Analyzer-Video** aufzeichnen aus. Wenn Sie dazu aufgefordert werden, klicken Sie auf **Fortfahren**.
1.  Klicken Sie in der oberen Menüleiste auf **Speichern**.

![Gif, das zeigt, wie man eine Topologie hinzufügt](./media/use-visual-studio-code-extension/add-topology.gif)

Beachten Sie, dass jetzt ein Eintrag in der Liste **Pipelinetopologien** auf der linken Seite mit der Bezeichnung **CVRToVideoSink vorhanden** ist. Dies ist eine Pipelinetopologie, in der einige der Parameter als Variablen definiert sind.

## <a name="create-a-live-pipeline"></a>Erstellen einer Livepipeline

Eine Livepipeline ist eine Instanz einer Pipelinetopologie. Die Variablen in einer Pipelinetopologie werden gefüllt, wenn eine Livepipeline erstellt wird.

1.  Klicken Sie links unter **Pipelinetopologien** mit der rechten Maustaste auf **CVRToVideoSink** und wählen Sie die Option zum **Erstellen einer Pipelinetopologie** aus.
1.  Geben Sie als **Instanzname** hier `livePipeline1` ein.
1. Geben Sie im Abschnitt **Parameter** unter dem **RtspUrl-Parameter** hier `rtsp://rtspsim:554/media/camera-300s.mkv` ein.
1.  Klicken Sie rechts oben auf die Option zum **Speichern und Aktivieren**.

![Gif über das Erstellen und Aktivieren einer Livepipeline](./media/use-visual-studio-code-extension/create-and-activate.gif)

Nachdem eine Livepipeline aktiviert wurde, können Betriebsereignisse angezeigt werden, indem Sie in der IoT Hub-Erweiterung auf die Schaltfläche **Überwachung des integrierten Ereignisendpunkts Starten** klicken, wie im Tutorial [Kontinuierliche Videoaufzeichnung und -wiedergabe](./use-continuous-video-recording.md#prepare-to-monitor-the-modules) gezeigt.

## <a name="next-steps"></a>Nächste Schritte

Laden Sie eine weitere der Beispielpipelinetopologien über die Visual Studio Code-Erweiterung, und zeigen Sie die Eigenschaften der einzelnen Knoten an. Informationen zum Parametrisieren von Variablen und zum Verbinden von Modulen finden Sie in der Referenz zur [Visual Studio Code-Erweiterung für den  Azure Video Analyzer](../visual-studio-code-extension.md).
