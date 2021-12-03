---
title: Analysieren von Livevideostreams mit mehreren KI-Modellen mithilfe der KI-Komposition
description: Dieser Artikel enthält eine Anleitung zur Analyse von Livevideostreams mit mehreren KI-Modellen mithilfe des Features zur KI-Komposition von Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d4d54fba6f3b59c0e32286333694fa460cdb07b3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487536"
---
# <a name="analyze-live-video-streams-with-multiple-ai-models-using-ai-composition"></a>Analysieren von Livevideostreams mit mehreren KI-Modellen mithilfe der KI-Komposition

[!INCLUDE [header](includes/edge-env.md)]

Bestimmte Kundenszenarien erfordern, dass Videos mit mehreren KI-Modellen analysiert werden. Diese Modelle können sich entweder [gegenseitig ergänzen](../ai-composition-overview.md#sequential-ai-composition) oder [unabhängig voneinander parallel](../ai-composition-overview.md#parallel-ai-composition) an [demselben Videostream arbeiten, oder es kann eine Kombination](../ai-composition-overview.md#combined-ai-composition) aus solchen ergänzten und unabhängig voneinander parallel arbeitenden Modellen auf denselben Videostream einwirken, um verwertbare Erkenntnisse zu gewinnen.

Azure Video Analyzer unterstützt solche Szenarien über ein Feature namens [KI-Komposition](../ai-composition-overview.md). In diesem Leitfaden erfahren Sie, wie Sie mehrere Modelle auf ergänzende Weise auf denselben Videostream anwenden können. Er verwendet parallel ein „Tiny (Light) YOLO“- und ein „reguläres YOLO“-Modell, um ein relevantes Objekt zu erkennen. Das „Tiny YOLO“-Modell ist rechnerisch weniger komplex, aber auch weniger genau als das YOLO-Modell und wird zuerst aufgerufen. Überschreitet das erkannte Objekt einen bestimmten Konfidenzschwellenwert, wird das sequentiell gestaffelte reguläre YOLO-Modell nicht aufgerufen, wodurch die zugrunde liegenden Ressourcen effizient genutzt werden.

Nach Abschluss der Schritte in diesem Leitfaden sind Sie in der Lage, einen simulierten Livevideostream durch eine Pipeline mit KI-Kombinierbarkeit ausführen zu lassen und sie auf Ihre spezifischen Szenarien zu erweitern. Diese Pipeline ist im folgenden Diagramm grafisch dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-ai-composition/motion-with-object-detection-using-ai-composition.svg" alt-text="KI-Komposition: Übersicht":::
 
## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    > [!NOTE]
    > Sie benötigen ein Azure-Abonnement mit Berechtigungen zum Erstellen von Dienstprinzipalen. (Die Rolle „Besitzer“ stellt diese Berechtigungen bereit.) Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt.
* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Vergewissern Sie sich, dass für das Netzwerk, mit dem Ihr Entwicklungscomputer verbunden ist, AMQP (Advanced Message Queueing Protocol) über Port 5671 für ausgehenden Datenverkehr zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.
* Schließen Sie den Schnellstart [Analysieren von Livevideos mithilfe eines eigenen gRPC-Modells](analyze-live-video-use-your-model-grpc.md) ab. Überspringen Sie diesen Vorgang nicht, da dies eine strikte Anforderung für die Schrittanleitung ist.

> [!TIP]
> Bei der Installation der Azure IoT Tools-Erweiterung werden Sie unter Umständen aufgefordert, Docker zu installieren. Sie können diese Aufforderung ignorieren.
>
> Falls für erstellte Azure-Ressourcen Probleme auftreten, helfen Ihnen die Informationen zum Beheben von häufigen Problemen im [Leitfaden für die Problembehandlung](troubleshoot.md#common-error-resolutions) weiter.

## <a name="review-the-video-sample"></a>Überprüfen des Videobeispiels

Da Sie den im Abschnitt „Voraussetzungen“ beschriebenen Schnellstart bereits abgeschlossen haben, haben Sie bereits ein Edgegerät erstellt. Dieses Edgegerät verfügt über den folgenden Eingabeordner – „/home/localedgeuser/samples/input“ – der bestimmte Videodateien enthält. Melden Sie sich am IoT Edge-Gerät an, wechseln Sie in das Verzeichnis: „home/localedgeuser/samples/input/“, und führen Sie den folgenden Befehl aus, um die Eingabedatei zu erhalten, die wir für diesen Leitfaden verwenden werden.

wget https://avamedia.blob.core.windows.net/public/co-final.mkv

Wenn Sie möchten, können Sie auf Ihrem Computer, auf dem der [VLC Media Player](https://www.videolan.org/vlc/) installiert ist, auch die Tastenkombination STRG+N auswählen und dann einen Link zu einem [Beispielvideo (MKV)](https://avamedia.blob.core.windows.net/public/co-final.mkv) einfügen, um die Wiedergabe zu starten. Sie sehen das Videomaterial von Autos auf einer Autobahn.

## <a name="create-and-deploy-the-pipeline"></a>Erstellen und Bereitstellen der Pipeline

Ähnlich wie bei den Schritten im Schnellstart, die Sie in den Voraussetzungen ausgeführt haben, können Sie die Schritte hier mit kleinen Anpassungen ausführen.

1. Befolgen Sie die Anleitungen im Abschnitt [Erstellen und Bereitstellen der Pipeline](analyze-live-video-use-your-model-grpc.md#create-and-deploy-the-pipeline) des soeben abgeschlossenen Schnellstarts. Stellen Sie sicher, dass Sie die folgenden Einstellungen vornehmen, während Sie mit den Schritten fortfahren. Diese Schritte tragen dazu bei, dass der richtige Textkörper für die direkten Methodenaufrufe verwendet wird.
    
    Bearbeiten Sie die Datei *operations.json*:
    
    * Ändern Sie den Link zur Pipelinetopologie: `"pipelineTopologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/ai-composition/topology.json"`
    * Unter `livePipelineSet`  
       1. stellen Sie Folgendes sicher: `"topologyName" : "AIComposition"` und 
       2. Ändern Sie den Wert von Parameter `rtspUrl` in `"rtsp://rtspsim:554/media/co-final.mkv"`.
        
    * Ändern Sie unter `pipelineTopologyDelete` den Namen: `"name" : "AIComposition"`
2. Befolgen Sie die Anleitungen im Abschnitt [Generieren und Bereitstellen des IoT Edge-Bereitstellungsmanifests](analyze-live-video-use-your-model-grpc.md#generate-and-deploy-the-iot-edge-deployment-manifest), verwenden Sie jedoch stattdessen das folgende Bereitstellungsmanifest – src/edge/deployment.composite.template.json.
3. Befolgen Sie die Anleitungen im Abschnitt [Ausführen des Beispielprogramms](analyze-live-video-use-your-model-grpc.md#run-the-sample-program).
4. Ergebnisdetails finden Sie im Abschnitt [Interpretieren der Ergebnisse](analyze-live-video-use-your-model-grpc.md#interpret-results). Zusätzlich zu den Analyseereignissen auf dem Hub und den Diagnoseereignissen erstellt die von Ihnen verwendete Topologie auch einen entsprechenden Videoclip in der Cloud, der durch die signalbasierte KI-Aktivierung des Signalgates ausgelöst wird. Dieser Clip wird auch von [Betriebsereignissen](record-event-based-live-video.md#operational-events) auf dem Hub begleitet, die von Downstreamworkflows übernommen werden können. Sie können den Videoclip [untersuchen und wiedergeben](record-event-based-live-video.md#playing-back-the-recording), indem Sie sich beim Azure-Portal anmelden.

## <a name="clean-up"></a>Bereinigen

Wenn Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie die in dieser Schnellstartanleitung erstellten Ressourcen löschen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Diagnosemeldungen](monitor-log-edge.md).
