---
title: Visual Studio Code-Erweiterung für Azure Video Analyzer
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der Visual Studio Code-Erweiterung für Azure Video Analyzer erläutert.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 06/01/2021
ms.openlocfilehash: e3d118f2651e7b680b85bbb41bb6ecc3d7106bba
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745687"
---
# <a name="quickstart-azure-video-analyzer-visual-studio-code-extension"></a>Schnellstart: Verwenden der Visual Studio Code-Erweiterung für Azure Video Analyzer

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Visual Studio Code-Erweiterung für Video Analyzer einrichten, mit Ihrem Video Analyzer-Edge-Modul verbinden und eine exemplarische Pipelinetopologie bereitstellen.  Dazu wird die gleiche Pipeline verwendet wie in der Schnellstartanleitung zu den ersten Schritten.  

Nach Abschluss der Einrichtungsschritte können Sie den simulierten Livevideostream über eine Livepipeline ausführen, die Bewegungen im Stream erkennt und meldet. Diese Pipeline ist im folgenden Diagramm grafisch dargestellt.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/get-started-detect-motion-emit-events/motion-detection.svg" alt-text="Bewegungserkennung":::
 
 ## <a name="prerequisites"></a>Voraussetzungen
 
* Ein Azure-Konto das ein aktives Abonnement beinhaltet. [Erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keines besitzen.

* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Video Analyzer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.azure-video-analyzer)

* Wenn Sie die Schnellstartanleitung [Erste Schritte: Azure Video Analyzer](./get-started-detect-motion-emit-events.md) nicht abgeschlossen haben, müssen Sie [Azure-Ressourcen einrichten](#set-up-azure-resources).    

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

[!INCLUDE [resources](./includes/common-includes/azure-resources.md)]

## <a name="connect-the-azure-video-analyzer-visual-studio-code-extension-to-your-iot-hub"></a>Herstellen einer Verbindung zwischen der Visual Studio Code-Erweiterung für Azure Video Analyzer und Ihrer IoT Hub-Instanz

Um die Erweiterung mit dem Edge-Modul zu verbinden, müssen Sie zuerst Ihre Verbindungszeichenfolge abrufen. Gehen Sie dazu wie folgt vor:

1.  Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihre IoT Hub-Instanz aus.
1.  Wählen Sie links unter `Settings` die Option `Shared access policies` aus.
1.  Wählen Sie den Richtliniennamen `iothubowner` aus.
1.  Kopieren Sie im Fenster auf der rechten Seite die primäre Verbindungszeichenfolge (`Primary connection string`).

Nachdem Sie nun über die Verbindungszeichenfolge verfügen, können Sie die folgenden Schritte ausführen, um die Erweiterung mit dem Edge-Modul zu verbinden:

1.  Wählen Sie in Visual Studio Code auf der linken Seite das Symbol `Azure Video Analyzer` aus.
1.  Klicken Sie auf die Schaltfläche `Enter Connection String`.
1.  Fügen Sie im oberen Bereich die Verbindungszeichenfolge aus dem Portal ein.
1.  Wählen Sie das Gerät aus. Der Standardwert ist `avasample-iot-edge-device`.
1.  Wählen Sie das Video Analyzer-Modul aus. Der Standardwert ist `avaedge`.

Auf der linken Seite wird nun Ihr verbundenes Gerät mit dem zugrunde liegenden Modul angezeigt.  Standardmäßig sind keine Pipelinetopologien bereitgestellt.

## <a name="deploy-a-topology-and-live-pipeline"></a>Bereitstellen einer Topologie und einer Livepipeline

Pipelinetopologien sind die Grundbausteine, die von Video Analyzer verwendet werden, um die Vorgehensweise zu definieren.  Weitere Informationen zu Pipelinetopologien finden Sie [hier](./pipeline.md).  In diesem Abschnitt wird eine Pipelinetopologie (also eine Vorlage) bereitgestellt und anschließend eine Instanz der Topologie (oder Livepipeline) erstellt. Die Livepipeline ist mit dem eigentlichen Videostream verbunden.

1.  Klicken Sie auf der linken Seite unter `Modules` mit der rechten Maustaste auf `Pipeline topologies`, und wählen Sie `Create pipeline topology` aus.
1.  Wählen Sie im oberen Bereich unter `Try sample topologies` > `Motion Detection` die Option `Publish motion events to IoT Hub` aus.  Klicken Sie auf `Proceed`, wenn Sie dazu aufgefordert werden.
1.  Klicken Sie rechts oben auf `Save`.

In der Liste `Pipeline topologies` auf der linken Seite sollte nun der Eintrag `MotionDetection` angezeigt werden.  Dies ist eine Pipelinetopologie, in der einige der Parameter als Variablen definiert sind, die Sie beim Erstellen einer Livepipeline einfügen können.  Erstellen Sie als Nächstes eine Livepipeline.

1.  Klicken Sie auf der linken Seite unter `Pipeline topologies` mit der rechten Maustaste auf `MotionDetection`, und wählen Sie `Create live pipeline` aus.
1.  Geben Sie für `Live pipeline name` den Namen `mdpipeline1` ein.
1.  Gehen Sie im `Parameters`-Abschnitt folgendermaßen vor:
    - Geben Sie für „rtspPassword“ entsprechend „testpassword“ ein.
    - Geben Sie für „rtspUrl“ die URL „rtsp://rtspsim:554/media/camera-300s.mkv“ ein.
    - Geben Sie für „rtspUserName“ entsprechend „testuser“ ein.
1.  Klicken Sie rechts oben auf die Option zum Speichern und Aktivieren.

Daraufhin wird eine anfängliche Topologie bereitgestellt, und auf Ihrem Edgegerät wird eine Livepipeline eingerichtet und verfügbar gemacht.  Wenn Sie die Azure IoT Hub-Erweiterung aus der Schnellstartanleitung zu den ersten Schritten installiert haben, können Sie den integrierten Ereignisendpunkt in der Visual Studio Code-Erweiterung für Azure IoT Hub überwachen, um die im Abschnitt [Überprüfen der Ergebnisse](./get-started-detect-motion-emit-events.md#observe-results) gezeigte Überwachung durchzuführen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [prerequisites](./includes/common-includes/clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den verschiedenen Funktionen der Visual Studio Code-Erweiterung für Azure Video Analytics finden Sie [hier](./visual-studio-code-extension.md).
