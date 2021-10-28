---
title: 'Schnellstart: Verbinden eines NXP MIMXRT1060-EVK mit Azure IoT Central'
description: Verwenden Sie eingebettete Azure RTOS-Software, um ein NXP MIMXRT1060-EVK-Gerät mit Azure IoT zu verbinden und Telemetriedaten zu senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/04/2021
ms.openlocfilehash: 68021567ee454c0479130d8c476b11c975d0012d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242178"
---
# <a name="quickstart-connect-an-nxp-mimxrt1060-evk-evaluation-kit-to-iot-central"></a>Schnellstart: Verbinden eines NXP MIMXRT1060-EVK Evaluation-Kits mit IoT Central

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtdauer**: 30 Minuten

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK)

In diesem Schnellstart verwenden Sie Azure RTOS, um ein NXP MIMXRT1060-EVK Evaluation-Kit (im weiteren Verlauf als NXP EVK bezeichnet) mit Azure IoT zu verbinden.

Sie führen die folgenden Aufgaben aus:

* Installieren von eingebetteten Entwicklungstools für die Programmierung eines NXP EVK in C
* Erstellen eines Images und Einspielen auf das NXP EVK
* Verwenden von Azure IoT Central zum Erstellen von Cloudkomponenten, Anzeigen von Eigenschaften und Gerätetelemetriedaten und Aufrufen von direkten Befehlen

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Windows 10
* [Git](https://git-scm.com/downloads) zum Klonen des Repositorys
* Hardware

    * Das [NXP MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) (NXP EVK)
    * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker
    * Kabelgebundener Ethernet-Zugriff
    * Ethernet-Kabel

## <a name="prepare-the-development-environment"></a>Vorbereiten der Entwicklungsumgebung

Zum Einrichten Ihrer Entwicklungsumgebung klonen Sie zuerst ein GitHub-Repository, in dem alle für den Schnellstart benötigten Ressourcen enthalten sind. Anschließend installieren Sie einige Programmiertools.

### <a name="clone-the-repo-for-the-quickstart"></a>Klonen des Repositorys für den Schnellstart

Klonen Sie das folgende Repository, um den gesamten Beispielgerätecode, die Setupskripts und die Offlineversionen der Dokumentation herunterzuladen. Wenn Sie dieses Repository bereits im Rahmen einer anderen Schnellstartanleitung geklont haben, müssen Sie diesen Schritt nicht erneut ausführen.

Führen Sie den folgenden Befehl aus, um das Repository zu klonen:

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installieren der Tools

Das geklonte Repository enthält ein Setupskript, mit dem die erforderlichen Tools installiert und konfiguriert werden. Wenn Sie diese Tools im Rahmen einer anderen Schnellstartanleitung für ein eingebettetes Gerät installiert haben, müssen Sie diesen Schritt nicht erneut ausführen.

> [!NOTE]
> Mit dem Setupskript werden die folgenden Tools installiert:
> * [CMake](https://cmake.org): Buildvorgang
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): Kompiliervorgang
> * [Termite](https://www.compuphase.com/software_termite.htm): Überwachung der Ausgabe des seriellen Anschlusses für verbundene Geräte

So installieren Sie die Tools

1. Navigieren Sie im Datei-Explorer zum folgenden Pfad im Repository, und führen Sie das Setupskript mit dem Namen *get-toolchain.bat* aus:

    *getting-started\tools\get-toolchain.bat*

1. Öffnen Sie nach der Installation ein neues Konsolenfenster, um die vom Setupskript vorgenommenen Konfigurationsänderungen zu überprüfen. Verwenden Sie diese Konsole für die Ausführung der restlichen Programmieraufgaben in der Schnellstartanleitung. Sie können die Windows-Eingabeaufforderung, PowerShell oder Git Bash für Windows verwenden.
1. Führen Sie den folgenden Code aus, um zu überprüfen, ob Version 3.14 (oder höher) von CMake installiert ist.

    ```shell
    cmake --version
    ```

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Zum Verbinden des NXP EVK mit Azure ändern Sie eine Konfigurationsdatei für Azure IoT-Einstellungen, erstellen das Image neu und übertragen es auf das Gerät.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\NXP\MIMXRT1060-EVK\app\azure_config.h*

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben.

    |Konstantenname|Wert|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Ihr Wert für den ID-Bereich*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Ihr Wert für die Geräte-ID*}|
    |`IOT_DEVICE_SAS_KEY` |{*Ihr Wert für den Primärschlüssel*}|

1. Speichern und schließen Sie die Datei.

### <a name="build-the-image"></a>Erstellen des Image

1. Führen Sie in Ihrer Konsole oder im Datei-Explorer das Skript *rebuild.bat* unter dem folgenden Pfad aus, um das Image zu erstellen:

    *getting-started\NXP\MIMXRT1060-EVK\tools\rebuild.bat*

2. Vergewissern Sie sich nach Abschluss des Buildvorgangs, dass die Binärdatei unter dem folgenden Pfad erstellt wurde:

    *getting-started\NXP\MIMXRT1060-EVK\build\app\mimxrt1060_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Suchen Sie am NXP EVK nach der Schaltfläche **Reset**, dem Micro-USB-Anschluss und dem Ethernet-Anschluss.  Sie verwenden diese Komponenten in den folgenden Schritten. Alle drei Komponenten sind in der folgenden Abbildung gekennzeichnet:

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/nxp-evk-board.png" alt-text="Ermitteln von Schlüsselkomponenten auf dem NXP EVK-Board":::

1. Schließen Sie das Micro-USB-Kabel an den Micro-USB-Anschluss des NXP EVK und dann an Ihren Computer an. Nach dem Einschalten des Geräts wird dieser Zustand durch eine LED angezeigt, die dauerhaft grün leuchtet.
1. Verwenden Sie das Ethernet-Kabel, um das NXP EVK mit einem Ethernet-Anschluss zu verbinden.
1. Suchen Sie im Datei-Explorer nach der Binärdatei, die Sie im vorherigen Abschnitt erstellt haben.
1. Kopieren Sie die Binärdatei *mimxrt1060_azure_iot.bin*.
1. Suchen Sie im Datei-Explorer nach dem NXP EVK-Gerät, das mit Ihrem Computer verbunden wurde. Das Gerät wird auf Ihrem System als Laufwerk mit der Bezeichnung **RT1060-EVK** angezeigt.
1. Fügen Sie die Binärdatei in den Stammordner des NXP EVK ein. Der Flashvorgang wird automatisch gestartet und ist nach wenigen Sekunden abgeschlossen.

    > [!NOTE]
    > Während des Flashvorgangs blinkt eine rote LED auf dem NXP EVK in schneller Folge.

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.
    > [!TIP]
    > Wenn beim Initialisieren oder Verbinden Ihres Geräts nach dem Einspielen per Flashvorgang Probleme auftreten, helfen Ihnen die Informationen zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md) weiter.
1. Wählen Sie **Settings** aus.
1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port** (Anschluss): Der Anschluss, mit dem Ihr NXP EVK verbunden ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie „OK“ aus.
1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Taste ist auf dem Gerät entsprechend beschriftet und befindet sich in der Nähe des Micro-USB-Anschlusses.
1. Überprüfen Sie in der **Termite**-App die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.19
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 20, 2021 19:41:20.319 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsg;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

## <a name="verify-the-device-status"></a>Überprüfen des Gerätestatus

Zeigen Sie den Gerätestatus im IoT Central-Portal wie folgt an:
1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Vergewissern Sie sich, dass der **Gerätestatus** auf **Bereitgestellt** aktualisiert wurde.
1. Vergewissern Sie sich, dass die Anzeige unter **Gerätevorlage** auf **Leitfaden für erste Schritte** aktualisiert wurde.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-view-status.png" alt-text="Screenshot: Gerätestatus in IoT Central":::

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Mit IoT Central können Sie den Weg der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen.

Zeigen Sie die Telemetriedaten im IoT Central-Portal wie folgt an:

1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Wählen Sie das Gerät in der Geräteliste aus.
1. Zeigen Sie die Telemetriedaten auf der Registerkarte **Übersicht** an, während das Gerät Nachrichten an die Cloud sendet.
1. Die Temperatur wird über den Mikrocontroller-Wafer gemessen.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Sie können auch mithilfe von IoT Central eine direkte Methode aufrufen, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, mit der Sie eine LED ein- oder ausschalten können.

Rufen Sie eine Methode wie folgt im IoT Central-Portal auf:

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehl** aus.
1. Wählen Sie in der Dropdownliste **Status** die Option **True** und dann **Ausführen** aus. Es gibt keine Änderung auf dem Gerät, da keine LED für das Umschalten verfügbar ist. Sie können jedoch die Ausgabe in Termite anzeigen, um den Status der Methoden zu überwachen.

    :::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-invoke-method.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Wählen Sie in der Dropdownliste **Status** die Option **False** und dann **Ausführen** aus.

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

Sie können die Geräteinformationen in IoT Central anzeigen.

Wählen Sie auf der Geräteseite die Registerkarte **Info** aus.

:::image type="content" source="media/quickstart-devkit-nxp-mimxrt1060-evk/iot-central-device-about.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, lesen Sie [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).

Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie über das IoT Central-Portal löschen.

Entfernen Sie die gesamte Azure IoT Central-Beispielanwendung und alle zugehörigen Geräte und Ressourcen wie folgt:
1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein benutzerdefiniertes Image mit Azure RTOS-Beispielcode erstellt und es anschließend per Flashvorgang auf dem NXP EVK-Gerät eingespielt. Außerdem haben Sie über das IoT Central-Portal Azure-Ressourcen erstellt, das NXP EVK auf sichere Weise mit Azure verbunden, die Telemetriedaten angezeigt und Nachrichten gesendet.

Lesen Sie als Nächstes die folgenden Artikel, um mehr darüber zu erfahren, wie Geräte mithilfe der IoT-Geräte-SDKs mit Azure IoT verbunden werden. 

> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Mit Azure RTOS werden für OEMs Komponenten bereitgestellt, mit denen diese die Kommunikation schützen, Code erstellen und die Datenisolation sicherstellen können, indem sie die zugrunde liegenden MCU/MPU-Mechanismen für den Hardwareschutz verwenden. Letztendlich ist aber jeder OEM selbst dafür verantwortlich sicherzustellen, dass sein Gerät die sich ständig weiter entwickelnden Sicherheitsanforderungen erfüllt.

