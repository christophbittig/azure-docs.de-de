---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: a7d745c4e2dfd364e9cb0d62225abf74bd1741a5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861163"
---
* Ein Azure-Konto das ein aktives Abonnement beinhaltet. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    > [!NOTE]
    > Sie benötigen ein Azure-Abonnement mit mindestens der Rolle „Mitwirkender“. Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen die richtigen Berechtigungen erteilt.
    * [Visual Studio Code](https://code.visualstudio.com/) mit den folgenden Erweiterungen:
        * [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

        [!INCLUDE [install-docker-prompt](../../common-includes/install-docker-prompt.md)]
        * [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
    * [Python 3](https://www.python.org/downloads/) (3.6.9 oder höher), [Pip 3](https://pip.pypa.io/en/stable/installing/) und optional [venv](https://docs.python.org/3/library/venv.html)
* Lesen Sie die Informationen unter [Schnellstart: Erkennen von Bewegung und Ausgeben von Ereignissen](../../../detect-motion-emit-events-quickstart.md).
## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

[![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)  
[!INCLUDE [resources](../../../includes/common-includes/azure-resources.md)]

## <a name="overview"></a>Überblick
In dieser Schnellstartanleitung verwenden Sie Video Analyzer, um Objekte wie Fahrzeuge und Personen zu erkennen. Sie veröffentlichen zugeordnete Rückschlussereignisse auf IoT Edge Hub.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./../../../media/analyze-live-video-use-your-model-http/overview.png" alt-text="Veröffentlichen zugeordneter Rückschlussereignisse in IoT Edge-Hub":::

In dem oben gezeigten Diagramm ist der Signalfluss dieser Schnellstartanleitung dargestellt. Ein [Edge-Modul](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) simuliert eine IP-Kamera, die einen RTSP-Server (Real-Time Streaming Protocol) hostet. Ein [RTSP-Quellknoten](./../../../../pipeline.md#rtsp-source) ruft den Videofeed per Pullvorgang von diesem Server ab und sendet Videoframes an den [Knoten des HTTP-Erweiterungsprozessors](./../../../../pipeline.md#http-extension-processor).

Der HTTP-Erweiterungsknoten übernimmt dabei die Rolle eines Proxys. Dabei werden die eingehenden Videoframes, die durch das Feld „samplingOptions“ festgelegt wurden, abgetastet und in den angegebenen Bildtyp umgewandelt. Anschließend werden die Bilder über REST an ein anderes Edge-Modul weitergeleitet, von dem ein KI-Modell hinter einem HTTP-Endpunkt ausgeführt wird. In diesem Beispiel wird dieses Edge-Modul unter Verwendung des YOLOv3-Modells erstellt, mit dem viele Objekttypen erkannt werden können. Der Knoten des HTTP-Erweiterungsprozessors erfasst die Erkennungsergebnisse und veröffentlicht Ereignisse im Knoten der [IoT Hub-Nachrichtensenke](./../../../../pipeline.md#iot-hub-message-sink). Der Knoten sendet diese Ereignisse dann an den [IoT Edge Hub-](../../../../../../iot-fundamentals/iot-glossary.md?view=iotedge-2018-06&preserve-view=true#iot-edge-hub).

In diesem Schnellstart führen Sie folgende Schritte aus:

* Erstellen und Bereitstellen der Livepipeline
* Interpretieren der Ergebnisse
* Bereinigen der Ressourcen
## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung
[!INCLUDE [setup development environment](./../../../includes/set-up-dev-environment/python/python-set-up-dev-env.md)]
