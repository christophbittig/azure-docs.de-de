---
title: Anzeigen des RTSP-Videostreams von Azure Percept DK
description: Hier erfahren Sie, wie Sie den RTSP-Videostream von Azure Percept DK anzeigen.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to
ms.openlocfilehash: 10d836e6d77f83e70efa1fc7757c8d8dd1d3f3f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221630"
---
# <a name="view-your-azure-percept-dk-rtsp-video-stream"></a>Anzeigen des RTSP-Videostreams von Azure Percept DK

In dieser Anleitung erfahren Sie, wie Sie den RTSP-Videostream von Azure Percept DK in Azure Percept Studio anzeigen. Rückschlüsse von KI-Modellen für maschinelles Sehen, die auf Ihrem Gerät bereitgestellt wurden, können im Webstream angezeigt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure Percept DK-Setup:](./quickstart-percept-dk-set-up.md) Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.

## <a name="view-the-rtsp-video-stream"></a>Anzeigen des RTSP-Videostreams

1. Schalten Sie Ihr DevKit ein.

1. Navigieren Sie zu [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicken Sie auf der linken Seite der Übersicht auf **Geräte**.

    :::image type="content" source="./media/how-to-view-video-stream/overview-devices-inline.png" alt-text="Übersichtsbildschirm von Azure Percept Studio" lightbox="./media/how-to-view-video-stream/overview-devices.png":::

1. Wählen Sie in der Liste Ihr DevKit aus.

    :::image type="content" source="./media/how-to-view-video-stream/select-device.png" alt-text="Screenshot: Verfügbare Geräte in Azure Percept Studio":::

1. Klicken Sie auf **View your device stream** (Gerätedatenstrom anzeigen).

    :::image type="content" source="./media/how-to-view-video-stream/view-device-stream.png" alt-text="Screenshot: Geräteseite mit verfügbaren Aktionen eines Projekts für maschinelles Sehen":::

    Daraufhin wird eine separate Registerkarte mit dem Livewebstream von Azure Percept DK angezeigt.

    :::image type="content" source="./media/how-to-view-video-stream/webstream.png" alt-text="Screenshot: Webstream des Geräts":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie Ihre [Azure Percept DK-Telemetriedaten](./how-to-view-telemetry.md) anzeigen.