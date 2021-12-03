---
title: Verbinden von Kameras mit dem Azure Video Analyzer-Dienst
description: In diesem Artikel werden Möglichkeiten zum direkten Verbinden von Kameras mit dem Azure Video Analyzer-Dienst erläutert.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f463fafb4f11e935d45c42326c23e1a0e4ffb28f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297695"
---
# <a name="connect-cameras-to-the-cloud"></a>Verbinden von Kameras mit der Cloud

[!INCLUDE [header](includes/cloud-env.md)]

Mit dem Azure Video Analyzer-Dienst können Benutzer RTSP-Kameras direkt mit der Cloud verbinden, um Videos mithilfe von [Livepipelines](../pipeline.md)zu erfassen und aufzuzeichnen. Dies verringert entweder die Rechenlast auf einem Edgegerät oder entfällt die Notwendigkeit eines Edgegeräts vollständig. Der Video Analyzer-Dienst unterstützt derzeit drei verschiedene Methoden zum Verbinden von Kameras mit der Cloud: Herstellen einer Verbindung über einen Remotegeräteadapter, Herstellen einer Verbindung hinter einer Firewall mithilfe eines IoT PnP-Befehls und Herstellen einer Verbindung über das Internet ohne Firewall.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/connect-cameras-to-cloud/connect-cameras-to-cloud.svg" alt-text="3 verschiedene Methoden zum Verbinden von Kameras mit der Cloud":::

## <a name="connect-via-a-remote-device-adapter"></a>Verbinden über einen Remotegeräteadapter

Sie können das Video Analyzer-Edgemodul auf einem IoT Edge Gerät im gleichen (privaten) Netzwerk wie die RTSP-Kameras bereitstellen und das Edgegerät mit dem Internet verbinden. Das Edgemodul kann jetzt als *Adapter* eingerichtet werden, mit dem der Video Analyzer-Dienst eine Verbindung mit den *Remotegeräten* (Kameras) herstellen kann. Das Edgemodul fungiert als [transparentes Gateway ](../../../iot-edge/iot-edge-as-gateway.md) für Videodatenverkehr zwischen den RTSP-Kameras und dem Video Analyzer-Dienst. Dieses Feature ist in den folgenden Szenarien nützlich:

* Wenn Kameras/Geräte vor der Internetverbindung geschützt werden müssen
* Wenn Kameras/Geräte nicht über die Funktionalität zum unabhängigen Herstellen einer Verbindung mit IoT Hub verfügen
* Wenn Energie, Speicherplatz oder andere Überlegungen zulassen, dass nur ein einfaches Edgegerät lokal bereitgestellt wird

Das Video Analyzer-Edgemodul fungiert nicht als transparentes Gateway für Messaging und Telemetriedaten von der Kamera bis IoT Hub, sondern nur als transparentes Gateway für Videos.

## <a name="connect-behind-a-firewall-using-an-iot-pnp-device-implementation"></a>Verbinden hinter einer Firewall mithilfe einer IoT PnP-Geräteimplementierungen

This method allows RTSP cameras or devices to connect to Video Analyzer behind a firewall using [IoT Plug and Play command interface](../../../iot-develop/overview-iot-plug-and-play.md) and eliminates the need for an edge device. Diese Methode erfordert, dass eine geeignete IoT PnP-Geräteimplementierungen installiert und auf Kameras oder Geräten ausgeführt werden. Informationen zum Verbinden kompatibler Geräte von einem beliebigen Hersteller finden Sie [hier](connect-devices.md)

## <a name="connect-over-the-internet-no-firewall"></a>Verbinden über das Internet (keine Firewall)

Diese Methode sollte nur für überwachte Proof-of-Concept-Übungen verwendet werden, bei denen es zulässig sein kann, dem Video Analyzer-Dienst den Zugriff auf das Gerät über das Internet ohne Firewall zu erlauben. 

Ein ähnlicher Anwendungsfall ist gegeben, wenn ein Modul auf einem virtuellen Azure-Computer bereitgestellt wird, um eine RTSP-Kamera zu simulieren, wie in dieser [Schnellstartanleitung](get-started-livepipelines-portal.md) beschrieben.


## <a name="next-steps"></a>Nächste Schritte

- Befolgen Sie [diese Anleitung](use-remote-device-adapter.md), um Kameras über einen Remotegeräteadapter zu verbinden.
- Befolgen Sie [diese Anleitung](connect-devices.md), um Informationen zum Verbinden von Geräten mithilfe einer IoT PnP-Implementierung zu erfahren.
- Befolgen Sie [diese Schnellstartanleitung](get-started-livepipelines-portal.md), um Kameras über das Internet zu verbinden.
