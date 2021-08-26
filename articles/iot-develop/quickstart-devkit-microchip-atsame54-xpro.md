---
title: Eine Schnellstartanleitung für das Verbinden eines Microchip ATSAME54-XPro mit Azure IoT Central
description: Mithilfe einer eingebetteter Azure RTOS-Software können Sie ein Microchip ATSAME54-XPro-Gerät mit Azure IoT verbinden und Telemetriedaten senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/08/2021
ms.openlocfilehash: 9611f80c6895387e71cd6387736cb1445e6b1542
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575308"
---
# <a name="quickstart-connect-a-microchip-atsame54-xpro-evaluation-kit-to-iot-central"></a>Ein Schnellstart für das Verbinden eines Microchip ATSAME54-XPro Evaluation Kits mit IoT Central

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtzeit der Fertigstellung**: 45 Minuten

[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO)

In dieser Schnellstartanleitung verwenden Sie Azure RTOS, um den Microchip ATSAME54-XPro (im Folgenden der Microchip E54 genannt) mit Azure IoT zu verbinden.

Sie führen die folgenden Aufgaben aus:

* Installieren Sie einen Satz eingebetteter Entwicklungstools zum Programmieren eines Microchip E54 in C
* Erstellen Sie ein Images und spielen Sie es auf den Microchip E54 ein
* Verwenden von Azure IoT Central zum Erstellen von Cloudkomponenten, Anzeigen von Eigenschaften und Gerätetelemetriedaten und Aufrufen von direkten Befehlen

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Microsoft Windows 10
* [Git](https://git-scm.com/downloads) zum Klonen des Repositorys
* Hardware

    * Der [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
    * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker
    * Kabelgebundener Ethernet-Zugriff
    * Ethernet-Kabel
    * Optional: der [Wetterklick](https://www.mikroe.com/weather-click)-Sensor. Sie können diesen Sensor zu dem Gerät hinzufügen, um die Wetterbedingungen zu überwachen. Auch wenn Sie nicht über diesen Sensor verfügen, können Sie diese Schnellstartanleitung abschließen.
    * Optional: Der [MikroBUS Xplained Pro](https://www.microchip.com/Developmenttools/ProductDetails/ATMBUSADAPTER-XPRO) Adapter. Verwenden Sie diesen Adapter, um den Wetterklick-Sensor an den Microchip E54 anzuschließen. Auch wenn Sie nicht über den Sensor und diesen Adapter verfügen, können Sie diese Schnellstartanleitung abschließen.

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
So installieren Sie die restlichen Tools:

* Installieren Sie [Microchip Studio für AVR-&reg; und SAM-Geräte](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices#). Microchip Studio ist eine Geräteentwicklungsumgebung, die die Tools zum Programmieren und Einspielen von Microchip E54 enthält. In diesem Tutorial verwenden Sie Microchip Studio nur, um den Microchip E54 einzuspielen. Die Installation dauert einige Minuten und fordert Sie mehrmals auf, die Installation von Komponenten zu genehmigen.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Zum Verbinden des Microchip E54 mit Azure ändern Sie eine Konfigurationsdatei für die Azure IoT-Einstellungen, erstellen das Image neu und spielen das Image auf das Gerät ein.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben.

    |Konstantenname|Wert|
    |-------------|-----|
    |`IOT_DPS_ID_SCOPE` |{*Ihr Wert für den ID-Bereich*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Ihr Wert für die Geräte-ID*}|
    |`IOT_DEVICE_SAS_KEY` |{*Ihr Wert für den Primärschlüssel*}|

1. Speichern und schließen Sie die Datei.

### <a name="connect-the-device"></a>Verbinden des Geräts

1. Suchen Sie auf dem Microchip E54 die Schaltfläche **Zurücksetzen,** den **Ethernet**-Port und den Micro-USB-Port mit der Bezeichnung **Debug USB**. Jede der Komponenten ist in der folgenden Abbildung hervorgehoben:

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png" alt-text="Suchen Sie die wichtigen Komponenten auf dem Microchip E54 Evaluation Kit Board":::

1. Schließen Sie das Micro-USB-Kabel an den **Debug USB**-Port des Microchip E54 an and und verbinden Sie ihn dann mit Ihren Computer.
    > [!NOTE]
    >  Weitere Informationen zum Einrichten und den ersten Schritte mit dem Microchip E54 finden Sie optional in dem [Benutzerhandbuch für SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf).

1. Verwenden Sie das Ethernet-Kabel, um den Microchip E54 mit einem Ethernet-Anschluss zu verbinden.

### <a name="optional-install-a-weather-sensor"></a>Optional: Installieren eines Wettersensors

Führen Sie die Schritte in diesem Abschnitt aus, wenn Sie über den Wetterklick-Sensor und den MikroBUS Xplained Pro-Adapter verfügen. Fahren Sie andernfalls mit dem [Erstellen des Images](#build-the-image) fort. Sie können diese Schnellstartanleitung auch dann abschließen, wenn Sie nicht über einen Sensor verfügen. Der Beispielcode für das Gerät gibt simulierte Daten zurück, wenn kein echter Sensor vorhanden ist.

1. Wenn Sie über den Wetterklick-Sensor und den MikroBUS Xplained Pro-Adapter verfügen, installieren Sie sie, wie auf dem folgenden Foto gezeigt auf dem Microchip E54:

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/sam-e54-sensor.png" alt-text="Installieren des Wetterklick-Sensors und des MikroBUS Xplained Pro-Adapters auf dem Microchip E54":::

1. Öffnen Sie die zuvor bearbeitete Konfigurationsdatei erneut:

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Legen Sie den Wert der Konstante `__SENSOR_BME280__` auf *1* fest, wie im folgenden Code aus der Headerdatei gezeigt. Durch Festlegen dieses Werts kann das Gerät die echten Sensordaten des Wetterklick-Sensors verwenden.

    `#define __SENSOR_BME280__ 1`

1. Speichern und schließen Sie die Datei.

### <a name="build-the-image"></a>Erstellen des Image

1. Führen Sie in Ihrer Konsole oder im Datei-Explorer das Skript *rebuild.bat* unter dem folgenden Pfad aus, um das Image zu erstellen:

    *getting-started\Microchip\ATSAME54-XPRO\tools\rebuild.bat*

2. Vergewissern Sie sich nach Abschluss des Buildvorgangs, dass die Binärdatei unter dem folgenden Pfad erstellt wurde:

    *getting-started\Microchip\ATSAME54-XPRO\build\app\atsame54_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Öffnen Sie die **Windows Start > Microchip Studio-Eingabeaufforderungskonsole** und wechseln Sie zum Ordner der von Ihnen erstellten Binärdatei Microchip E54.

    *getting-started\Microchip\ATSAME54-XPRO\build\app*

1. Verwenden Sie das Hilfsprogramm *Atprogram*, um den Microchip E54 mit dem Binärbild zu einzuspielen:

    > [!NOTE] 
    > Weitere Informationen zur Verwendung der Atmel-ICE- und Atprogram-Tools mit dem Microchip E54 finden Sie in der Dokumentation [Verwenden von Atmel-ICE für die AVR-Programmierung in der Massenproduktion](http://ww1.microchip.com/downloads/en/AppNotes/00002466A.pdf).

    ```shell
    atprogram --tool edbg --interface SWD --device ATSAME54P20A program --chiperase --file atsame54_azure_iot.bin --verify
    ```

    Nach Abschluss des Einspielvorgangs bestätigt die Konsole, dass die Programmierung erfolgreich war:

    ```output
    Firmware check OK
    Programming and verification completed successfully.
    ```

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.    
    > [!TIP]
    > Wenn beim Initialisieren oder Verbinden Ihres Geräts nach dem Einspielen Probleme auftreten, helfen Ihnen die Informationen zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md) weiter.
1. Wählen Sie **Settings** aus.
1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port**: Der Porst, an dem Ihr Microchip E54 angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.
    * **Flusssteuerungsmodus**: DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie „OK“ aus.
1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Taste ist auf dem Gerät entsprechend beschriftet und befindet sich in der Nähe des Micro-USB-Anschlusses.
1. Überprüfen Sie in der **Termite**-App die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

    ```output
    Starting Azure thread

    Initializing DHCP
        IP address: 192.168.0.21
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 45.55.58.103
        SNTP time update: Jun 5, 2021 20:2:46.32 UTC 
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

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status.png" alt-text="Screenshot: Gerätestatus in IoT Central":::

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Mit IoT Central können Sie den Weg der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen.

Zeigen Sie die Telemetriedaten im IoT Central-Portal wie folgt an:

1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Wählen Sie das Gerät in der Geräteliste aus.
1. Zeigen Sie die Telemetriedaten auf der Registerkarte **Übersicht** an, während das Gerät Nachrichten an die Cloud sendet.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Darüber hinaus haben Sie auch die Möglichkeit, IoT Central zum Aufrufen einer direkten Methode zu verwenden, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, mit der Sie eine LED ein- oder ausschalten können.

Rufen Sie eine Methode wie folgt im IoT Central-Portal auf:

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehl** aus.
1. Wählen Sie in der Dropdownliste **Status** die Option **True** und dann **Ausführen** aus.  Die LED sollte aufleuchten.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Wählen Sie in der Dropdownliste **Status** die Option **False** und dann **Ausführen** aus. Die LED sollte nicht mehr aufleuchten.

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

Sie können die Geräteinformationen in IoT Central anzeigen.

Wählen Sie auf der Geräteseite die Registerkarte **Info** aus.

:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, helfen Ihnen die Informationen zur [Problembehandlung](troubleshoot-embedded-device-quickstarts.md) weiter.

Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Schnellstart erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie über das IoT Central-Portal löschen.

Entfernen Sie die gesamte Azure IoT Central-Beispielanwendung und alle zugehörigen Geräte und Ressourcen wie folgt:
1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie ein benutzerdefiniertes Image mit dem Azure RTOS-Beispielcode erstellt und es anschließend auf dem Microchip E54-Gerät eingespielt. Außerdem haben Sie das IoT Central-Portal verwendet, um Azure-Ressourcen zu erstellen, den Microchip E54 auf sichere Weise mit Azure zu verbinden, die Telemetriedaten anzuzeigen und Nachrichten zu senden.

Lesen Sie als Nächstes die folgenden Artikel, um mehr darüber zu erfahren, wie Geräte mithilfe der IoT-Geräte-SDKs mit Azure IoT verbunden werden. 

> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines simulierten Geräts mit IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Mit Azure RTOS werden für OEMs Komponenten bereitgestellt, mit denen diese die Kommunikation schützen, Code erstellen und die Datenisolation sicherstellen können, indem sie die zugrunde liegenden MCU/MPU-Mechanismen für den Hardwareschutz verwenden. Letztendlich ist aber jeder OEM selbst dafür verantwortlich sicherzustellen, dass sein Gerät die sich ständig weiter entwickelnden Sicherheitsanforderungen erfüllt.

