---
title: Bereitstellen eines Vision-KI-Modells in Azure Percept DK
description: Hier erfahren Sie, wie Sie ein Vision-KI-Modell in Azure Percept DK über Azure Percept Studio bereitstellen.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/12/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 55948f2beede0597a30dd557a82ee297c6a540ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006371"
---
# <a name="deploy-a-vision-ai-model-to-azure-percept-dk"></a>Bereitstellen eines Vision-KI-Modells in Azure Percept DK

Befolgen Sie diese Anleitung, um ein Vision-KI-Modell für Azure Percept DK über Azure Percept Studio bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure Percept DK-Setup:](./quickstart-percept-dk-set-up.md) Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.

## <a name="model-deployment"></a>Modellimplementierung

1. Schalten Sie Ihr DevKit ein.

1. Navigieren Sie zu [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klicken Sie auf der linken Seite der Übersicht auf **Geräte**.

    :::image type="content" source="./media/how-to-deploy-model/overview-devices-inline.png" alt-text="Übersichtsbildschirm von Azure Percept Studio" lightbox="./media/how-to-deploy-model/overview-devices.png":::

1. Wählen Sie in der Liste Ihr DevKit aus.

    :::image type="content" source="./media/how-to-deploy-model/select-device.png" alt-text="Liste der Percept-Geräte":::

1. Klicken Sie auf der nächsten Seite auf **Beispielmodell bereitstellen**, wenn Sie eines der vortrainierten Vision-Beispielmodelle bereitstellen möchten. Wenn Sie eine vorhandene [benutzerdefinierte Vision-Lösung ohne Code](./tutorial-nocode-vision.md) bereitstellen möchten, klicken Sie auf **Custom Vision-Projekt bereitstellen**.

    :::image type="content" source="./media/how-to-deploy-model/deploy-model.png" alt-text="Modellauswahl für die Bereitstellung":::

1. Wenn Sie sich für die Bereitstellung einer Vision-Lösung ohne Code entschieden haben, wählen Sie Ihr Projekt und die gewünschte Modelliteration aus, und klicken Sie auf **Bereitstellen**.

1. Wenn Sie sich für die Bereitstellung eines Beispielmodells entschieden haben, wählen Sie das Modell aus, und klicken Sie auf **Auf Gerät bereitstellen**.

1. Ist die Modellimplementierung erfolgreich, wird in der oberen rechten Ecke des Bildschirms eine Statusmeldung angezeigt. Wenn Sie Modellrückschlüsse in Aktion sehen möchten, klicken Sie in der Statusmeldung auf den Link **Stream anzeigen**, um den RTSP-Videostream vom Vision-SOM Ihres DevKits anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie Ihre [Azure Percept DK-Telemetriedaten](how-to-view-telemetry.md) anzeigen.
