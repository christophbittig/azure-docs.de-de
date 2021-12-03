---
title: GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Ihre virtuellen Computer vom Typ „Azure IoT Edge für Linux unter Windows“ für die Nutzung von Hostgeräte-GPUs konfigurieren.
author: v-tcassi
manager: kgremban
ms.author: v-tcassi
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c85659a6c9e89145494d4683da37bd2f13cbd379
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130234863"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>GPU-Beschleunigung für Azure IoT Edge für Linux unter Windows (Vorschau)

GPUs sind eine beliebte Wahl für Berechnungen im Rahmen der künstlichen Intelligenz, da sie über parallele Verarbeitungsfunktionen verfügen und die Ausführung von auf maschinellem Sehen basierenden Rückschlussvorgängen häufig schneller als mit CPUs ist. Zur besseren Unterstützung von KI- und Machine Learning-Anwendungen kann mit Azure IoT Edge für Linux unter Windows eine GPU für das Linux-Modul des virtuellen Computers verfügbar gemacht werden.

> [!NOTE]
> Die unten beschriebenen Features für die GPU-Beschleunigung befinden sich in der Vorschauphase und können jederzeit geändert werden.

Azure IoT Edge für Linux unter Windows unterstützt mehrere GPU-Passthrough-Technologien, z. B.:

* **Direkte Gerätezuweisung (Direct Device Assignment, DDA)** : GPU-Kerne werden entweder dem virtuellen Linux-Computer oder dem Host zugeordnet.

* **GPU-Paravirtualisierung (GPU-PV)** : Die GPU wird vom virtuellen Linux-Computer und vom Host gemeinsam genutzt.

Die Bereitstellung von Azure IoT Edge für Linux unter Windows wählt automatisch die richtige Passthrough-Methode aus, die zu den unterstützten Funktionen der GPU-Hardware Ihres Geräts passt.

> [!IMPORTANT]
> Diese Features können Komponenten enthalten, die von der NVIDIA Corporation oder ihren Lizenzgebern entwickelt wurden bzw. sich in deren Besitz befinden. Die Verwendung der Komponenten unterliegt den Lizenzbedingungen von NVIDIA, die auf der [NVIDIA-Website](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us) angegeben sind.
>
> Indem Sie die Features für die GPU-Beschleunigung verwenden, akzeptieren Sie die Lizenzbedingungen von NVIDIA und stimmen ihnen zu.

## <a name="prerequisites"></a>Voraussetzungen

Für die Features für die GPU-Beschleunigung von Azure IoT Edge für Linux unter Windows wird derzeit nur speziell ausgewählte GPU-Hardware unterstützt. Je nach Ihrer Konfiguration benötigen Sie bei Verwendung dieses Features ggf. auch den aktuellen Dev Channel-Build für Windows-Insiders.

Hier sind die unterstützten GPUs und die erforderlichen Windows-Versionen angegeben:

* NVIDIA T4 (Unterstützung von DDA)

  * Windows Server, Build 17763 oder höher
  * Windows Enterprise oder Professional, Build 21318 oder höher (Windows-Insider-Build)

* NVIDIA GeForce/Quadro (Unterstützung von GPU-PV)

  * Windows Enterprise oder Professional, Build 20145 oder höher (Windows-Insider-Build)

### <a name="windows-insider-builds"></a>Windows-Insider-Builds

Für Benutzer mit Windows Enterprise oder Professional müssen Sie die [Registrierung für das Windows-Insider-Programm](https://insider.windows.com/getting-started#register) durchführen.

Befolgen Sie nach der Registrierung die Anleitung auf der Registerkarte **2. Flight**, um Zugriff auf den passenden Windows-Insider-Build zu erhalten. Wählen Sie den [Dev Channel](/windows-insider/flight-hub/#active-development-builds-of-windows-10) aus. Nach der Installation können Sie die Versionsnummer des Builds überprüfen, indem Sie `winver` über die Eingabeaufforderung ausführen.

### <a name="t4-gpus"></a>T4-GPUs

Für **T4-GPUs** empfiehlt Microsoft die Installation eines Treibers für die Risikominderung des Geräts, den Sie beim Anbieter Ihrer GPU erhalten. Die Installation eines Treibers für die Risikominderung des Geräts ist zwar optional, aber Sie können damit ggf. die Sicherheit Ihrer Bereitstellung erhöhen. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver).

> [!WARNING]
> Das Aktivieren von Hardwaregeräte-Passthrough kann zu einem erhöhten Sicherheitsrisiko führen. Microsoft empfiehlt zur Risikominderung die Installation eines Gerätetreibers, den Sie vom Anbieter Ihrer GPU erhalten. Weitere Informationen finden Sie unter [Bereitstellen von Grafikgeräten mit Discrete Device Assignment](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda).

### <a name="geforcequadro-gpus"></a>GeForce/Quadro-GPUs

Laden Sie für **GeForce/Quadro-GPUs** den [NVIDIA CUDA-fähigen Treiber für das Windows-Subsystem für Linux (WSL)](https://developer.nvidia.com/cuda/wsl) herunter, und installieren Sie ihn, um ihn für Ihre vorhandenen CUDA-ML-Workflows zu nutzen. Die „CUDA for WSL“-Treiber wurden ursprünglich für WSL entwickelt, aber sie werden auch für Azure IoT Edge für Linux unter Windows eingesetzt.

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>Verwenden der GPU-Beschleunigung für Ihre Linux-Bereitstellung unter Windows

Nun ist alles bereit, um die Linux-Module mit GPU-Beschleunigung in Ihrer Windows-Umgebung über Azure IoT Edge für Linux unter Windows bereitzustellen und auszuführen. Weitere Informationen zum Bereitstellungsprozess finden Sie im „[Leitfaden für die Bereitstellung eines einzelnen IoT Edge für Linux unter Windows-Geräts mithilfe von symmetrischen Schlüsseln](how-to-provision-single-device-linux-on-windows-symmetric.md) oder [mithilfe von X.509-Zertifikaten](how-to-provision-single-device-linux-on-windows-x509.md)“.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Ihrer Bereitstellung von Azure IoT Edge für Linux unter Windows](how-to-install-iot-edge-on-windows.md)

* Probieren Sie unser [GPU-fähiges Beispiel mit Vision on Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md) aus. Hierbei handelt es sich um eine Lösungsvorlage, die veranschaulicht, wie Sie basierend auf dem maschinellen Lernen Ihre eigene Machine Learning-Anwendung erstellen.

* Weitere Informationen zu GPU-Passthrough-Technologien finden Sie in der [DDA-Dokumentation](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda) und im [GPU-PV-Blogbeitrag](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization).
