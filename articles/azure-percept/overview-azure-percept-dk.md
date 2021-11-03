---
title: Übersicht über Azure Percept DK- und Vision-Geräte
description: Informieren Sie sich ausführlicher über Azure Percept DK und Azure Percept Vision.
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: 'template-concept #Required, leave this attribute/value as-is., ignite-fall-2021'
ms.openlocfilehash: 3ce11bcd6b50b4dae9f63c1f9ce4c24cdc49f61d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069746"
---
# <a name="azure-percept-dk-and-vision-device-overview"></a>Übersicht über Azure Percept DK- und Vision-Geräte

Azure Percept DK ist ein Development Kit für Edge-KI, das für die Entwicklung von Lösungen im Zusammenhang mit KI für maschinelles Sehen und Audio mit [Azure Percept Studio](./overview-azure-percept-studio.md) konzipiert ist. Azure Percept DK kann im [Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270) erworben werden.

> [!div class="nextstepaction"]
> [Jetzt kaufen](https://go.microsoft.com/fwlink/p/?LinkId=2155270)

</br>

> [!VIDEO https://www.youtube.com/embed/Qj8NGn-7s5A]

## <a name="key-features"></a>Wichtige Features

- Ausführung von KI am Edge Durch die integrierte Hardwarebeschleunigung kann das Development Kit KI-Modelle ohne eine Cloudverbindung ausführen.

- Integrierter Hardwarestamm für Vertrauenssicherheit. Hier finden Sie weitere Informationen zur [Sicherheit von Azure Percept](./overview-percept-security.md).

- Nahtlose Integration in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) und andere Azure-Dienste wie Azure IoT Hub, Azure Cognitive Services und [Live Video Analytics](../azure-video-analyzer/video-analyzer-docs/overview.md).

- Kompatibel mit [Azure Percept Audio](./overview-azure-percept-audio.md), ein optionales Zubehör zum Entwickeln von KI-Audiolösungen.

- Unterstützung für KI-Tools von Drittanbietern, wie z. B. ONNX und TensorFlow.

- Integration in das 80/20-Schienensystem, das unbegrenzte Geräte-Montagekonfigurationen ermöglicht. Weitere Informationen zur 80/20-Integration finden Sie [hier](./overview-8020-integration.md).

## <a name="hardware-components"></a>Hardwarekomponenten

- Azure Percept DK-Trägerplatine:
    - NXP iMX8m-Prozessor
    - TPM (Trusted Platform Module), Version 2.0
    - WLAN- und Bluetooth-Konnektivität
    - Weitere Informationen finden Sie im [Datenblatt Azure Percept DK](./azure-percept-dk-datasheet.md) .

- Azure Percept Vision SoM (System on Module)
    - Intel Movidius Myriad X (MA2085) VPU (Vision Processing Unit)
    - RGB-Kamerasensor
    - Weitere Informationen finden Sie im [Datenblatt Azure Percept Vision](./azure-percept-vision-datasheet.md) .

## <a name="getting-started-with-azure-percept-dk"></a>Erste Schritte mit Azure Percept DK

- Ein Development Kit einrichten:
    - [Auspacken und Zusammensetzen des Azure Percept DK](./quickstart-percept-dk-unboxing.md)
    - [Abschließen Azure Percept DK-Einrichtungserfahrung](./quickstart-percept-dk-set-up.md)

- Beginnen Sie mit der Erstellung von Vision- und Audiolösungen:
    - [Erstellen einer Vision-Lösung ohne Code in Azure Percept Studio](./tutorial-nocode-vision.md)
    - [Erstellen einer Sprachlösung ohne Code in Azure Percept Studio](./tutorial-no-code-speech.md) (Azure Percept Audio-Zubehör erforderlich)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erwerben von Azure Percept DK im Microsoft-Onlinestore](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
