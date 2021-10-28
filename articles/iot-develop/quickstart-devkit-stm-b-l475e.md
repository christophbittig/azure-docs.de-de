---
title: 'Schnellstart: Herstellen einer Verbindung zwischen STMicroelectronics B-L475E-IOT01A oder B-L4S5I-IOT01A und Azure IoT Central'
description: Verwenden Sie die eingebettete Software von Azure RTOS, um eine Verbindung zwischen einem STMicroelectronics B-L475E-IOT01A- bzw. B-L4S5I-IOT01A-Gerät und Azure IoT herzustellen und Telemetriedaten zu senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/02/2021
ms.openlocfilehash: 30e64f70f713540574545200dc188342b63e2f01
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130242159"
---
# <a name="quickstart-connect-an-stmicroelectronics-b-l475e-iot01a-or-b-l4s5i-iot01a-discovery-kit-to-iot-central"></a>Schnellstart: Herstellen einer Verbindung zwischen einem STMicroelectronics B-L475E-IOT01A- oder B-L4S5I-IOT01A Discovery Kit und IoT Central

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtdauer**: 30 Minuten

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4+)

In diesem Schnellstart stellen Sie mithilfe von Azure RTOS eine Verbindung zwischen dem STMicroelectronics [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html)- bzw. [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) Discovery Kit (nachfolgend als „STM-DevKit“ bezeichnet) und Azure IoT her.

Sie führen die folgenden Aufgaben aus:

* Installieren von eingebetteten Entwicklungstools für die Programmierung des STM-DevKit in C
* Erstellen eines Images und Einspielen auf das STM-DevKit
* Verwenden von Azure IoT Central zum Erstellen von Cloudkomponenten, Anzeigen von Eigenschaften und Gerätetelemetriedaten und Aufrufen von direkten Befehlen

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Windows 10
* [Git](https://git-scm.com/downloads) zum Klonen des Repositorys
* Hardware

    * Entweder [B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) oder [B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) (STM-DevKit)
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

Zum Verbinden des STM-DevKit mit Azure bearbeiten Sie eine Konfigurationsdatei mit WLAN- und Azure IoT-Einstellungen, erstellen das Image neu und spielen das Image per Flashvorgang auf dem Gerät ein.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\STMicroelectronics\STM32L4_L4+\app\azure_config.h*

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

    *getting-started\STMicroelectronics\STM32L4_L4+\tools\rebuild.bat*

2. Vergewissern Sie sich nach der Erstellung, dass zwei Binärdateien erstellt wurden. Für jedes STM-DevKit muss ein binäres Image erstellt worden sein. Beim Erstellen werden die Images in den folgenden Verzeichnissen gespeichert:

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l475_azure_iot.bin*

    *getting-started\STMicroelectronics\STM32L4_L4+\build\app\stm32l4S5_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Suchen Sie am STM-DevKit-MCU nach der Taste **Reset** (Zurücksetzen), nach dem Micro-USB-Anschluss mit der Bezeichnung **USB STLink** und nach der Teilenummer des Boards. Diese Elemente werden Sie in den nächsten Schritten benötigen. Sie sind in der folgenden Abbildung hervorgehoben:

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/stm-devkit-board.png" alt-text="Ermitteln der wichtigen Komponenten auf dem STM-DevKit-Board":::

1. Schließen Sie das Micro-USB-Kabel an den **USB STLINK**-Anschluss des STM-DevKit und dann an Ihren Computer an.

    > [!NOTE]
    > Ausführliche Setupinformationen zum STM-DevKit finden Sie in der Anleitung auf der Verpackung oder unter [Ressourcen zu B-L475E-IOT01A](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html#resource) oder unter [Ressourcen zu B-L4S5I-IOT01A](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html#resource).

1. Suchen Sie im Datei-Explorer nach den Binärdateien, die Sie im vorherigen Abschnitt erstellt haben.

1. Kopieren Sie die Binärdatei, deren Dateiname der Nummer des verwendeten STM-DevKit entspricht. Wenn die Teilenummer des Boards beispielsweise **B-L475E-IOT01A1** lautet, kopieren Sie die Binärdatei mit dem Namen *stm32l475_azure_iot.bin*.

1. Suchen Sie im Datei-Explorer nach dem STM-DevKit, das am Computer angeschlossen ist. Das Gerät wird auf Ihrem System als Laufwerk mit der Bezeichnung **DIS_L4IOT** angezeigt.

1. Fügen Sie die Binärdatei in den Stammordner des STM-DevKit ein. Der Flashvorgang wird automatisch gestartet und ist nach wenigen Sekunden abgeschlossen.

    > [!NOTE]
    > Während des Flashvorgangs leuchtet eine LED am STM-DevKit im Wechsel rot und grün auf.

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.
    > [!TIP]
    > Falls Sie für Termite keine Verbindung mit Ihrem DevKit herstellen können, sollten Sie den [ST-LINK-Treiber](https://www.st.com/en/development-tools/stsw-link009.html) installieren und es anschließend erneut versuchen. Weitere Schritte finden Sie auf der Seite zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).
1. Wählen Sie **Settings** aus.
1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port** (Anschluss): Der Anschluss, an dem Ihr STM-DevKit angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie „OK“ aus.
1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Schaltfläche ist schwarz und auf dem Gerät gekennzeichnet.
1. Überprüfen Sie in der **Termite**-App die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

    ```output
    Starting Azure thread

    Initializing WiFi
        Module: ISM43362-M3G-L44-SPI
        MAC address: C4:7F:51:8F:67:F6
        Firmware revision: C3.5.2.5.STM
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.22
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 108.62.122.57
        SNTP time update: May 21, 2021 22:42:8.394 UTC 
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
    > [!IMPORTANT]
    > Wenn bei der Initialisierung des DNS-Clients ein Fehler auftritt und Sie darüber informiert werden, das die WLAN-Firmware veraltet ist, müssen Sie die Firmware des WLAN-Moduls aktualisieren. Laden Sie das [Firmwareupdate des WLAN-Moduls Inventek ISM 43362](https://www.st.com/resource/en/utilities/inventek_fw_updater.zip) herunter, und installieren Sie es. Drücken Sie anschließend die Taste **Reset** (Zurücksetzen) am Gerät, um die Verbindung erneut zu überprüfen, und fahren Sie mit dieser Schnellstartanleitung fort.


Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

## <a name="verify-the-device-status"></a>Überprüfen des Gerätestatus

Zeigen Sie den Gerätestatus im IoT Central-Portal wie folgt an:
1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Vergewissern Sie sich, dass der **Gerätestatus** auf **Bereitgestellt** aktualisiert wurde.
1. Vergewissern Sie sich, dass die Anzeige unter **Gerätevorlage** auf **Leitfaden für erste Schritte** aktualisiert wurde.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-view-status.png" alt-text="Screenshot: Gerätestatus in IoT Central":::

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Mit IoT Central können Sie den Weg der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen.

Zeigen Sie die Telemetriedaten im IoT Central-Portal wie folgt an:

1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Wählen Sie das Gerät in der Geräteliste aus.
1. Zeigen Sie die Telemetriedaten auf der Registerkarte **Übersicht** an, während das Gerät Nachrichten an die Cloud sendet.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Darüber hinaus haben Sie auch die Möglichkeit, IoT Central zum Aufrufen einer direkten Methode zu verwenden, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, mit der Sie eine LED ein- oder ausschalten können.

Rufen Sie eine Methode wie folgt im IoT Central-Portal auf:

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehl** aus.
1. Wählen Sie in der Dropdownliste **Status** die Option **True** und dann **Ausführen** aus.  Die LED sollte aufleuchten.

    :::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-invoke-method.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Wählen Sie in der Dropdownliste **Status** die Option **False** und dann **Ausführen** aus. Die LED sollte nicht mehr aufleuchten.

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

Sie können die Geräteinformationen in IoT Central anzeigen.

Wählen Sie auf der Geräteseite die Registerkarte **Info** aus.

:::image type="content" source="media/quickstart-devkit-stm-b-l475e/iot-central-device-about.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, lesen Sie [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).

Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie über das IoT Central-Portal löschen.

Entfernen Sie die gesamte Azure IoT Central-Beispielanwendung und alle zugehörigen Geräte und Ressourcen wie folgt:
1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein benutzerdefiniertes Image mit Azure RTOS-Beispielcode erstellt und es anschließend per Flashvorgang auf dem STM-DevKit-Gerät eingespielt. Außerdem haben Sie das IoT Central-Portal verwendet, um Azure-Ressourcen zu erstellen, das STM-DevKit auf sichere Weise mit Azure zu verbinden, die Telemetriedaten anzuzeigen und Nachrichten zu senden.

Lesen Sie als Nächstes die folgenden Artikel, um mehr darüber zu erfahren, wie Geräte mithilfe der IoT-Geräte-SDKs mit Azure IoT verbunden werden. 

> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Hub](quickstart-send-telemetry-iot-hub.md)


> [!IMPORTANT]
> Mit Azure RTOS werden für OEMs Komponenten bereitgestellt, mit denen diese die Kommunikation schützen, Code erstellen und die Datenisolation sicherstellen können, indem sie die zugrunde liegenden MCU/MPU-Mechanismen für den Hardwareschutz verwenden. Letztendlich ist aber jeder OEM selbst dafür verantwortlich sicherzustellen, dass sein Gerät die sich ständig weiter entwickelnden Sicherheitsanforderungen erfüllt.



