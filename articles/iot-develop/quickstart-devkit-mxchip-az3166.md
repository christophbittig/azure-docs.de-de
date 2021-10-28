---
title: 'Schnellstart: Verbinden eines MXCHIP AZ3166 mit Azure IoT Central'
description: Verwenden Sie eingebettete Azure RTOS-Software, um ein MXCHIP AZ3166-Gerät mit Azure IoT zu verbinden und Telemetriedaten zu senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 073b8f4efd7651a8ee56b54319d36122cf0e9bc1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226298"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-central"></a>Schnellstart: Verbinden eines MXCHIP AZ3166-DevKit mit IoT Central

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtdauer**: 30 Minuten

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

In diesem Schnellstart verwenden Sie Azure RTOS, um ein MXCHIP AZ3166-IoT-DevKit (nachfolgend als „MXCHIP-DevKit“ bezeichnet) mit Azure IoT zu verbinden.

Sie führen die folgenden Aufgaben aus:

* Installieren von eingebetteten Entwicklungstools für die Programmierung eines MXCHIP-DevKit in C
* Erstellen eines Images und Einspielen auf das MXCHIP-DevKit
* Verwenden von Azure IoT Central zum Erstellen von Cloudkomponenten, Anzeigen von Eigenschaften und Gerätetelemetriedaten und Aufrufen von direkten Befehlen

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Windows 10
* [Git](https://git-scm.com/downloads) zum Klonen des Repositorys
* Hardware

    * [MXCHIP AZ3166-IoT-DevKit](https://www.seeedstudio.com/AZ3166-IOT-Developer-Kit.html) (MXCHIP-DevKit)
    * WLAN, 2,4 GHz
    * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker

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

Zum Verbinden des MXCHIP-DevKit mit Azure bearbeiten Sie eine Konfigurationsdatei mit WLAN- und Azure IoT-Einstellungen, erstellen das Image neu und spielen das Image per Flashvorgang auf dem Gerät ein.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. Legen Sie die WLAN-Konstanten auf die folgenden Werte Ihrer lokalen Umgebung fest.

    |Konstantenname|Wert|
    |-------------|-----|
    |`WIFI_SSID` |{*Ihre WLAN-SSID*}|
    |`WIFI_PASSWORD` |{*Ihr WLAN-Kennwort*}|
    |`WIFI_MODE` |{*Einer der enumerierten WLAN-Moduswerte in der Datei*}|

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben.

    |Konstantenname|Wert|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Ihr Wert für den ID-Bereich*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Ihr Wert für die Geräte-ID*}|
    |`IOT_DEVICE_SAS_KEY` |{*Ihr Wert für den Primärschlüssel*}|

1. Speichern und schließen Sie die Datei.

### <a name="build-the-image"></a>Erstellen des Image

1. Führen Sie in Ihrer Konsole oder im Datei-Explorer das Skript *rebuild.bat* unter dem folgenden Pfad aus, um das Image zu erstellen:

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

1. Vergewissern Sie sich nach Abschluss des Buildvorgangs, dass die Binärdatei unter dem folgenden Pfad erstellt wurde:

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Suchen Sie auf dem MXCHIP-DevKit nach der Taste **Reset** und dem Micro-USB-Anschluss. Sie verwenden diese Komponenten in den folgenden Schritten. Die Komponenten sind in der folgenden Abbildung gekennzeichnet:

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/mxchip-iot-devkit.png" alt-text="Ermitteln der wichtigen Komponenten auf dem MXChip-DevKit-Board":::

1. Schließen Sie das Micro-USB-Kabel an den Micro-USB-Anschluss des MXCHIP-DevKit und dann an Ihren Computer an.
1. Suchen Sie im Datei-Explorer nach der Binärdatei, die Sie im vorherigen Abschnitt erstellt haben.
1. Kopieren Sie die Binärdatei *mxchip_azure_iot.bin*.
1. Suchen Sie im Datei-Explorer nach dem MXCHIP-DevKit-Gerät, das mit Ihrem Computer verbunden wurde. Das Gerät wird auf Ihrem System als Laufwerk mit der Bezeichnung **AZ3166** angezeigt.
1. Fügen Sie die Binärdatei in den Stammordner des MXCHIP-DevKit ein. Der Flashvorgang wird automatisch gestartet und ist nach wenigen Sekunden abgeschlossen.

    > [!NOTE]
    > Während des Flashvorgangs leuchtet auf dem MXCHIP-DevKit ein grüne LED auf.

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.
    > [!TIP]
    > Falls Sie für Termite keine Verbindung mit Ihrem DevKit herstellen können, sollten Sie den [ST-LINK-Treiber](https://www.st.com/en/development-tools/stsw-link009.html) installieren und es anschließend erneut versuchen. Weitere Schritte finden Sie auf der Seite zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).
1. Wählen Sie **Settings** aus.
1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port** (Anschluss): Der Anschluss, an dem Ihr MXCHIP-DevKit angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie „OK“ aus.
1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Taste ist auf dem Gerät entsprechend beschriftet und befindet sich in der Nähe des Micro-USB-Anschlusses.
1. Überprüfen Sie in der **Termite**-App die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 38.229.71.1
        SNTP time update: May 19, 2021 20:36:6.994 UTC 
    SUCCESS: SNTP initialized

    Initializing Azure IoT DPS client
        DPS endpoint: global.azure-devices-provisioning.net
        DPS ID scope: ***
        Registration ID: mydevice
    SUCCESS: Azure IoT DPS client initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

## <a name="verify-the-device-status"></a>Überprüfen des Gerätestatus

Zeigen Sie den Gerätestatus im IoT Central-Portal wie folgt an:
1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Vergewissern Sie sich, dass der **Gerätestatus** auf **Bereitgestellt** aktualisiert wurde.
1. Vergewissern Sie sich, dass die Anzeige unter **Gerätevorlage** auf **MXCHIP Getting Started Guide** (Leitfaden zu den ersten Schritten mit MXCHIP) aktualisiert wurde.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-view-status.png" alt-text="Screenshot: Gerätestatus in IoT Central":::

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Mit IoT Central können Sie den Weg der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen.

Zeigen Sie die Telemetriedaten im IoT Central-Portal wie folgt an:

1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Wählen Sie das Gerät in der Geräteliste aus.
1. Zeigen Sie die Telemetriedaten auf der Registerkarte **Übersicht** an, während das Gerät Nachrichten an die Cloud sendet.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Sie können auch mithilfe von IoT Central eine direkte Methode aufrufen, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, mit der Sie eine LED ein- oder ausschalten können.

Rufen Sie eine Methode wie folgt im IoT Central-Portal auf:

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehle** aus.
1. Wählen Sie in der Dropdownliste **Status** die Option **True** und dann **Ausführen** aus.  Die LED sollte aufleuchten.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-invoke-method.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Wählen Sie in der Dropdownliste **Status** die Option **False** und dann **Ausführen** aus. Die LED sollte nicht mehr aufleuchten.

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

Sie können die Geräteinformationen in IoT Central anzeigen.

Wählen Sie auf der Geräteseite die Registerkarte **Info** aus.

:::image type="content" source="media/quickstart-devkit-mxchip-az3166/iot-central-device-about.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, lesen Sie [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).

Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie über das IoT Central-Portal löschen.

Entfernen Sie die gesamte Azure IoT Central-Beispielanwendung und alle zugehörigen Geräte und Ressourcen wie folgt:
1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein benutzerdefiniertes Image mit Azure RTOS-Beispielcode erstellt und es anschließend per Flashvorgang auf dem MXCHIP DevKit-Gerät eingespielt. Außerdem haben Sie das IoT Central-Portal verwendet, um Azure-Ressourcen zu erstellen, das MXCHIP DevKit auf sichere Weise mit Azure zu verbinden, die Telemetriedaten anzuzeigen und Nachrichten zu senden.

Lesen Sie als Nächstes die folgenden Artikel, um mehr darüber zu erfahren, wie Geräte mithilfe der IoT-Geräte-SDKs mit Azure IoT verbunden werden. 

> [!div class="nextstepaction"]
> [Verbinden eines MXCHIP AZ3166-DevKits mit IoT Hub](quickstart-devkit-mxchip-az3166-iot-hub.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Mit Azure RTOS werden für OEMs Komponenten bereitgestellt, mit denen diese die Kommunikation schützen, Code erstellen und die Datenisolation sicherstellen können, indem sie die zugrunde liegenden MCU/MPU-Mechanismen für den Hardwareschutz verwenden. Letztendlich ist aber jeder OEM selbst dafür verantwortlich sicherzustellen, dass sein Gerät die sich ständig weiter entwickelnden Sicherheitsanforderungen erfüllt.

