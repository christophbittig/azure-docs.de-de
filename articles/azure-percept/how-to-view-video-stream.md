---
title: Anzeigen des RTSP-Videostreams von Azure Percept DK
description: Hier erfahren Sie, wie Sie den RTSP-Videostream von Azure Percept DK anzeigen.
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 19b47b0971ee59d7b029a7b6b0dde77a83d1447b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006238"
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
