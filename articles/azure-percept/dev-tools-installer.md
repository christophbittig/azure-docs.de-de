---
title: Installieren der Azure Percept-Entwicklungstools
description: Hier erfahren Sie, wie Sie die erweiterte Entwicklung mit Azure Percept mithilfe des Dev Tools-Paketinstallationsprogramms beschleunigen können.
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 05936b691fe6988959d7b743295049557e0c5479
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006599"
---
# <a name="install-azure-percept-development-tools"></a>Installieren der Azure Percept-Entwicklungstools

Das Dev Tools-Paketinstallationsprogramm ist eine Komplettlösung, die alle erforderlichen Tools für die Entwicklung einer fortschrittlichen Intelligent Edge-Lösung installiert und konfiguriert.

## <a name="mandatory-tools"></a>Obligatorische Tools

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3.6 oder höher](https://www.python.org/)
* [Docker 20.10](https://www.docker.com/)
* [PIP3 21.1](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 2.0](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1.2](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Optionale Tools

* [NVIDIA DeepStream SDK 5](https://developer.nvidia.com/deepstream-sdk) (Toolkit für die Entwicklung von Lösungen für NVIDIA-Beschleuniger)
* [Intel OpenVINO Toolkit 2021.3](https://docs.openvinotoolkit.org/) (Toolkit für die Entwicklung von Lösungen für Intel-Beschleuniger)
* [Lobe.ai 0.9](https://lobe.ai/)  
* [Streamlit 0.8](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) oder 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda 4.5](https://docs.conda.io/en/latest/miniconda.html)
* [Chainer 7.7](https://chainer.org/)
* [Caffe 1.0](https://caffe.berkeleyvision.org/)
* [CUDA Toolkit 11.2](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Bekannte Probleme

- Optionale Installationen von Caffe, NVIDIA DeepStream SDK und Intel OpenVINO Toolkit können fehlschlagen, wenn Docker nicht ordnungsgemäß ausgeführt wird. Um diese optionalen Tools zu installieren, stellen Sie sicher, dass Docker installiert ist und ausgeführt wird, bevor Sie versuchen, die Installationen über das Dev Tools-Paketinstallationsprogramm zu starten.

- Die Version des auf dem Mac installierten optionalen CUDA Toolkits ist 10.0.130. CUDA Toolkit 11 unterstützt die Entwicklung oder Ausführung von Anwendungen unter macOSity nicht mehr.

## <a name="docker-minimum-requirements"></a>Mindestanforderungen für Docker

### <a name="windows"></a>Windows

- Windows 10 64-Bit: Pro, Enterprise oder Education (Build 16299 oder höher).

- Die Windows-Features „Hyper-V“ und „Container“ müssen aktiviert sein. Die folgende Hardware ist für die erfolgreiche Ausführung von Hyper-V unter Windows 10 erforderlich:

    - 64-Bit-Prozessor mit [Second Level Address Translation (SLAT)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - 4 GB Systemarbeitsspeicher
    - Die Unterstützung für Hardwarevirtualisierung auf BIOS-Ebene muss in den BIOS-Einstellungen aktiviert sein. Weitere Informationen finden Sie unter „Virtualisierung“.

> [!NOTE]
> Docker unterstützt Docker Desktop unter Windows basierend auf dem Supportlebenszyklus von Microsoft für das Betriebssystem Windows 10. Weitere Informationen finden Sie im [Informationsblatt zum Lebenszyklus von Windows](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

Erfahren Sie mehr über das [Installieren von Docker Desktop unter Windows](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- Der Mac muss ein Modell von 2010 oder ein neueres Modell mit den folgenden Eigenschaften sein:
    - Intel-Prozessor
    - Intel-Hardwareunterstützung für die Virtualisierung der Speicherverwaltungseinheit (Memory Management Unit, MMU), einschließlich Extended Page Tables (EPT) und uneingeschränktem Modus. Sie können überprüfen, ob Ihr Computer über diese Unterstützung verfügt, indem Sie den folgenden Befehl in einem Terminal ausführen: ```sysctl kern.hv_support```. Wenn der Mac das Hypervisor-Framework unterstützt, gibt der Befehl ```kern.hv_support: 1``` aus.

- macOS-Version 10.14 oder höher (Mojave, Catalina oder Big Sur). Wir empfehlen, ein Upgrade auf die neueste macOS-Version durchzuführen. Wenn nach dem Upgrade auf macOS-Version 10.15 Probleme auftreten, müssen Sie die neueste Version von Docker Desktop installieren, um die Kompatibilität mit dieser macOS-Version sicherzustellen.

- Mindestens 4 GB RAM

- Installieren Sie KEINE VirtualBox-Versionen vor 4.3.30, da sie mit Docker Desktop nicht kompatibel sind.

- Das Installationsprogramm wird in Apple M1 nicht unterstützt.

Erfahren Sie mehr über das [Installieren von Docker Desktop unter Mac](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Starten Sie das Installationsprogramm.

Laden Sie das Dev Tools-Paketinstallationsprogramm für [Windows](https://go.microsoft.com/fwlink/?linkid=2132187), [Linux](https://go.microsoft.com/fwlink/?linkid=2132186) oder [Mac](https://go.microsoft.com/fwlink/?linkid=2132296) herunter. Starten Sie das Installationsprogramm gemäß ihrer Plattform, wie unten beschrieben.

### <a name="windows"></a>Windows

1. Klicken Sie auf **Dev-Tools-Pack-Installer**, um den Installations-Assistenten zu öffnen.

### <a name="mac"></a>Mac

1. Verschieben Sie die Datei **Dev-Tools-Pack-Installer.app** nach dem Herunterladen in den Ordner **Programme**.

1. Klicken Sie auf **Dev-Tools-Pack-Installer.app**, um den Installations-Assistenten zu öffnen.

1. Falls ein Sicherheitsdialogfeld „Nicht verifizierter Entwickler“ angezeigt wird, gehen Sie wie folgt vor:

    1. Wechseln Sie zu **Systemeinstellungen** -> **Sicherheit** -> **Allgemein**, und klicken Sie auf **Dennoch öffnen** (neben **Dev-Tools-Pack-Installer.app**).
    1. Klicken Sie auf das Electron-Symbol.
    1. Klicken Sie im Dialogfeld „Sicherheit“ auf **Öffnen**.

### <a name="linux"></a>Linux

1. Wenn Sie vom Browser dazu aufgefordert werden, klicken Sie auf **Speichern**, um den Download des Installationsprogramms abzuschließen.

1. Fügen Sie der **.appimage**-Datei Ausführungsberechtigungen hinzu:

    1. Öffnen Sie ein Linux-Terminal.

    1. Geben Sie im Terminal Folgendes ein, um zum Ordner **Downloads** zu wechseln:

        ```bash
        cd ~/Downloads/
        ```

    1. Erstellen Sie die ausführbare AppImage-Datei:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Führen Sie den Installer aus:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Fügen Sie der **.appimage**-Datei Ausführungsberechtigungen hinzu:

    1. Klicken Sie mit der rechten Maustaste auf die APPIMAGE-Datei, und wählen Sie **Eigenschaften** aus.
    1. Öffnen Sie die Registerkarte **Berechtigungen**.
    1. Aktivieren Sie das Kontrollkästchen neben **Ausführen der Datei als Programm zulassen**.
    1. Schließen Sie die **Eigenschaften**, und öffnen Sie die **.appimage**-Datei.

## <a name="run-the-installer"></a>Führen Sie das Installationsprogramm aus.

1. Klicken Sie auf der Seite zum **Installieren des Dev Tools-Paketinstallationsprogramms** auf **Lizenz anzeigen**, um die Lizenzverträge für die einzelnen Softwarepakete im Installationsprogramm anzuzeigen. Wenn Sie die Bedingungen der Lizenzverträge akzeptieren, aktivieren Sie das Kontrollkästchen, und klicken Sie auf **Weiter**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Bildschirm mit den Lizenzverträgen im Installationsprogramm.":::

1. Klicken Sie auf **Datenschutzbestimmungen**, um die Microsoft-Datenschutzbestimmungen anzuzeigen. Wenn Sie den Datenschutzbestimmungen zustimmen und Diagnosedaten an Microsoft senden möchten, wählen Sie **Ja** aus, und klicken Sie auf **Weiter**. Wählen Sie andernfalls **Nein** aus, und klicken Sie auf **Weiter**.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Bildschirm mit den Datenschutzbestimmungen im Installationsprogramm.":::

1. Wählen Sie auf der Seite **Komponenten konfigurieren** die optionalen Tools aus, die Sie installieren möchten (die erforderlichen Tools werden standardmäßig installiert).

    1. Wenn Sie mit dem Azure Percept-Audio-SOM (System On Module) arbeiten, das im Azure Percept DK enthalten ist, müssen Sie das Intel OpenVino Toolkit und Miniconda3 installieren.

    1. Klicken Sie auf **Installieren**, um die Installation fortzusetzen.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="Bildschirm mit den verfügbaren Softwarepaketen im Installationsprogramm.":::

1. Nach der erfolgreichen Installation aller ausgewählten Komponenten wird die Seite **Fertigstellen des Installations-Assistenten** angezeigt. Klicken Sie auf **Fertig stellen**, um das Installationsprogramm zu beenden.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="Bildschirm zum Abschluss des Installationsprogramms.":::

## <a name="docker-status-check"></a>Überprüfen des Docker-Status

Wenn Sie vom Installationsprogramm aufgefordert werden, die korrekte Ausführung von Docker Desktop zu überprüfen, führen Sie die folgenden Schritte aus:

### <a name="windows"></a>Windows

1. Erweitern Sie ausgeblendete Symbole in der Taskleiste.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Taskleiste":::

1. Überprüfen Sie, ob für das Docker Desktop-Symbol **Docker Desktop wird ausgeführt** angezeigt wird:

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-Status":::

1. Wenn das obige Symbol nicht in der Taskleiste angezeigt wird, starten Sie Docker Desktop über das Startmenü.

1. Falls Sie von Docker zum Neustarten aufgefordert werden, können Sie das Installationsprogramm nach dem Neustart schließen und neu starten, wenn Docker ausgeführt wird. Alle erfolgreich installierten Anwendungen von Drittanbietern sollten erkannt und nicht automatisch neu installiert werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten bei der erweiterten Entwicklung für Azure Percept DK finden Sie im [Repository zur erweiterten Entwicklung in Azure Percept](https://github.com/microsoft/azure-percept-advanced-development).
