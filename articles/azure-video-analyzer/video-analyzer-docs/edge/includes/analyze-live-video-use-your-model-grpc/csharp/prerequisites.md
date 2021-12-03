---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5b0f58607552d1891b116e463332f06ea8578b94
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861659"
---
* Ein Azure-Konto das ein aktives Abonnement beinhaltet. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    > [!NOTE]
    > Sie benötigen ein Azure-Abonnement mit mindestens der Rolle „Mitwirkender“. Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt.
* [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
    * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    
    [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-grpc/overview.png" alt-text="Übersicht über gRPC":::
 
In diesem Diagramm ist der Fluss der Signale in diesem Schnellstart dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Ein [RTSP-Quellknoten](./../../../../pipeline.md#rtsp-source) ruft den Videofeed per Pullvorgang von diesem Server ab und sendet Videoframes an den Knoten des [Bewegungserkennungsprozessors](./../../../../pipeline.md#motion-detection-processor). Dieser Prozessor erkennt Bewegung und überträgt bei Erkennung Videoframes an den Knoten des [gRPC-Erweiterungsprozessors](./../../../../pipeline.md#grpc-extension-processor).

Der gRPC-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Er wandelt die Video-Einzelbilder in den angegebenen Bildtyp um. Anschließend leitet er das Bild über gRPC an ein anderes Edge-Modul weiter, das ein KI-Modell hinter einem gRPC-Endpunkt über einen [gemeinsam genutzten Speicher](https://en.wikipedia.org/wiki/Shared_memory) ausführt. In diesem Beispiel wird dieses Edge-Modul unter Verwendung des [YOLOv3](https://github.com/Azure/video-analyzer/tree/main/edge-modules/extensions/yolo/yolov3)-Modells erstellt, mit dem viele Objekttypen erkannt werden können. Der Knoten des gRPC-Erweiterungsprozessors erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Senke](./../../../../pipeline.md#iot-hub-message-sink). Der Knoten sendet diese Ereignisse dann an den [IoT Edge Hub-](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub).

In diesem Schnellstart führen Sie folgende Schritte aus:

1. Erstellen und Bereitstellen der Pipeline
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
