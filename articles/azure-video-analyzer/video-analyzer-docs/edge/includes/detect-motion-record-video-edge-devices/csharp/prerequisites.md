---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: b3cb7d6bed2cbff05ec7572e08d997b657e44ba8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861500"
---
* Ein Azure-Konto das ein aktives Abonnement beinhaltet. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    [!INCLUDE [azure-subscription-permissions](../../common-includes/azure-subscription-permissions.md)]
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
> :::image type="content" source="./../../../media/detect-motion-record-video-edge-devices/overview.png" alt-text="Veröffentlichen zugeordneter Rückschlussereignisse in IoT Edge-Hub":::

Im obigen Diagramm ist der Fluss der Signale in dieser Schnellstartanleitung dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Der Knoten einer [RTSP-Quelle](./../../../../pipeline.md#rtsp-source) ruft den Videofeed per Pullvorgang von diesem Server ab und sendet Videoframes an den Knoten des [Bewegungserkennungsprozessors](./../../../../pipeline.md#motion-detection-processor). Die RTSP-Quelle sendet dieselben Videoframes an den Knoten eines [Signalgateprozessors](./../../../../pipeline.md#signal-gate-processor), der geschlossen bleibt, bis er durch ein Ereignis ausgelöst wird.

Wenn der Bewegungserkennungsprozessor im Video Bewegungen erkennt, sendet er ein Ereignis an den Knoten des Signalgateprozessors und löst ihn dadurch aus. Das Gate wird für den konfigurierten Zeitraum geöffnet und sendet Videoframes an den Knoten der [Dateisenke](./../../../../pipeline.md#file-sink). Von diesem Knoten der Dateisenke wird das Video als MP4-Datei im lokalen Dateisystem des Edgegeräts aufgezeichnet. Die Datei wird am konfigurierten Speicherort gespeichert.

In diesem Schnellstart führen Sie folgende Schritte aus:

1. Erstellen und Bereitstellen der Pipeline
1. Interpretieren der Ergebnisse
1. Bereinigen der Ressourcen

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/csharp/csharp-set-up-dev-env.md)]
