---
title: Eine Schnellstartanleitung für das Verbinden eines Microchip ATSAME54-XPro mit Azure IoT Central
description: Mithilfe einer eingebetteter Azure RTOS-Software können Sie ein Microchip ATSAME54-XPro-Gerät mit Azure IoT verbinden und Telemetriedaten senden.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 10/18/2021
zone_pivot_groups: iot-develop-toolset
ms.openlocfilehash: 2b2b0cdb4c81a8ca501c58fce9ddb4771a5bbaba
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167648"
---
# <a name="quickstart-connect-a-microchip-atsame54-xpro-evaluation-kit-to-iot-central"></a>Ein Schnellstart für das Verbinden eines Microchip ATSAME54-XPro Evaluation Kits mit IoT Central

**Gilt für**: [Entwicklung von eingebetteten Geräten](about-iot-develop.md#embedded-device-development)<br>
**Gesamtzeit der Fertigstellung**: 45 Minuten

:::zone pivot="iot-toolset-cmake"
[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO)
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
[![Code durchsuchen](media/common/browse-code.svg)](https://github.com/azure-rtos/samples/)
:::zone-end

In dieser Schnellstartanleitung verwenden Sie Azure RTOS, um den Microchip ATSAME54-XPro (im Folgenden der Microchip E54 genannt) mit Azure IoT zu verbinden.

Sie führen die folgenden Aufgaben aus:

* Installieren Sie einen Satz eingebetteter Entwicklungstools zum Programmieren eines Microchip E54 in C
* Erstellen Sie ein Images und spielen Sie es auf den Microchip E54 ein
* Verwenden von Azure IoT Central zum Erstellen von Cloudkomponenten, Anzeigen von Eigenschaften und Gerätetelemetriedaten und Aufrufen von direkten Befehlen

:::zone pivot="iot-toolset-cmake"

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
>
> * [CMake](https://cmake.org): Buildvorgang
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm): Kompiliervorgang
> * [Termite](https://www.compuphase.com/software_termite.htm): Überwachung der Ausgabe des seriellen Anschlusses für verbundene Geräte

So installieren Sie die Tools

1. Navigieren Sie im Datei-Explorer zum folgenden Pfad im Repository, und führen Sie das Setupskript mit dem Namen ***get-toolchain.bat*** aus:

    *getting-started\tools\get-toolchain.bat*

1. Öffnen Sie nach der Installation ein neues Konsolenfenster, um die vom Setupskript vorgenommenen Konfigurationsänderungen zu überprüfen. Verwenden Sie diese Konsole für die Ausführung der restlichen Programmieraufgaben in der Schnellstartanleitung. Sie können die Windows-Eingabeaufforderung, PowerShell oder Git Bash für Windows verwenden.
1. Führen Sie den folgenden Code aus, um zu überprüfen, ob Version 3.14 (oder höher) von CMake installiert ist.

    ```shell
    cmake --version
    ```

1. Installieren Sie [Microchip Studio für AVR-&reg; und SAM-Geräte](https://www.microchip.com/en-us/development-tools-tools-and-software/microchip-studio-for-avr-and-sam-devices#). Microchip Studio ist eine Geräteentwicklungsumgebung, die die Tools zum Programmieren und Einspielen von Microchip E54 enthält. In diesem Tutorial verwenden Sie Microchip Studio nur, um den Microchip E54 einzuspielen. Die Installation dauert einige Minuten und fordert Sie mehrmals auf, die Installation von Komponenten zu genehmigen.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Zum Verbinden des Microchip E54 mit Azure ändern Sie eine Konfigurationsdatei für die Azure IoT-Einstellungen, erstellen das Image neu und spielen das Image auf das Gerät ein.

### <a name="add-configuration"></a>Hinzufügen einer Konfiguration

1. Öffnen Sie die folgende Datei in einem Text-Editor:

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben.

    |Konstantenname|Wert|
    |-------------|-----|
    | `IOT_DPS_ID_SCOPE` | {*Ihr Wert für den ID-Bereich*} |
    | `IOT_DPS_REGISTRATION_ID` | {*Ihr Wert für die Geräte-ID*} |
    | `IOT_DEVICE_SAS_KEY` | {*Ihr Wert für den Primärschlüssel*} |

1. Speichern und schließen Sie die Datei.

### <a name="connect-the-device"></a>Verbinden des Geräts

1. Suchen Sie auf dem Microchip E54 die Schaltfläche **Zurücksetzen,** den **Ethernet**-Port und den Micro-USB-Port mit der Bezeichnung **Debug USB**. Jede der Komponenten ist in der folgenden Abbildung hervorgehoben:

    ![Suchen Sie die wichtigen Komponenten auf dem Microchip E54 Evaluation Kit Board](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Schließen Sie das Micro-USB-Kabel an den **Debug USB**-Port des Microchip E54 an and und verbinden Sie ihn dann mit Ihren Computer.

    > [!NOTE]
    > Weitere Informationen zum Einrichten und den ersten Schritte mit dem Microchip E54 finden Sie optional in dem [Benutzerhandbuch für SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf).

1. Verwenden Sie das Ethernet-Kabel, um den Microchip E54 mit einem Ethernet-Anschluss zu verbinden.

### <a name="optional-install-a-weather-sensor"></a>Optional: Installieren eines Wettersensors

Führen Sie die Schritte in diesem Abschnitt aus, wenn Sie über den Wetterklick-Sensor und den MikroBUS Xplained Pro-Adapter verfügen. Fahren Sie andernfalls mit dem [Erstellen des Images](#build-the-image) fort. Sie können diese Schnellstartanleitung auch dann abschließen, wenn Sie nicht über einen Sensor verfügen. Der Beispielcode für das Gerät gibt simulierte Daten zurück, wenn kein echter Sensor vorhanden ist.

1. Wenn Sie über den Wetterklick-Sensor und den MikroBUS Xplained Pro-Adapter verfügen, installieren Sie sie, wie auf dem folgenden Foto gezeigt auf dem Microchip E54:

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/sam-e54-sensor.png" alt-text="Installieren des Wetterklick-Sensors und des MikroBUS Xplained Pro-Adapters auf dem Microchip E54":::

1. Öffnen Sie die zuvor bearbeitete Konfigurationsdatei erneut:

    *getting-started\Microchip\ATSAME54-XPRO\app\azure_config.h*

1. Legen Sie den Wert der Konstante `__SENSOR_BME280__` auf **1** fest, wie im folgenden Code aus der Headerdatei gezeigt. Durch Festlegen dieses Werts kann das Gerät die echten Sensordaten des Wetterklick-Sensors verwenden.

    `#define __SENSOR_BME280__ 1`

1. Speichern und schließen Sie die Datei.

### <a name="build-the-image"></a>Erstellen des Image

1. Führen Sie in Ihrer Konsole oder im Datei-Explorer das Skript ***rebuild.bat*** unter dem folgenden Pfad aus, um das Image zu erstellen:

    *getting-started\Microchip\ATSAME54-XPRO\tools\rebuild.bat*

1. Vergewissern Sie sich nach Abschluss des Buildvorgangs, dass die Binärdatei unter dem folgenden Pfad erstellt wurde:

    *getting-started\Microchip\ATSAME54-XPRO\build\app\atsame54_azure_iot.bin*

### <a name="flash-the-image"></a>Einspielen des Images

1. Öffnen Sie die **Windows Start > Microchip Studio-Eingabeaufforderungskonsole** und wechseln Sie zum Ordner der von Ihnen erstellten Binärdatei Microchip E54.

    *getting-started\Microchip\ATSAME54-XPRO\build\app*

1. Verwenden Sie das Hilfsprogramm *Atprogram*, um den Microchip E54 mit dem Binärbild zu einzuspielen:

    ```shell
    atprogram --tool edbg --interface SWD --device ATSAME54P20A program --chiperase --file atsame54_azure_iot.bin --verify
    ```

    > [!NOTE]
    > Weitere Informationen zur Verwendung der Atmel-ICE- und Atprogram-Tools mit dem Microchip E54 finden Sie in der Dokumentation [Verwenden von Atmel-ICE für die AVR-Programmierung in der Massenproduktion](http://ww1.microchip.com/downloads/en/AppNotes/00002466A.pdf).

    Nach Abschluss des Einspielvorgangs bestätigt die Konsole, dass die Programmierung erfolgreich war:

    ```output
    Firmware check OK
    Programming and verification completed successfully.
    ```

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Sie können die **Termite**-App verwenden, um die Kommunikation zu überwachen und sich zu vergewissern, dass Ihr Gerät richtig eingerichtet wurde.

1. Starten Sie **Termite**.

    > [!TIP]
    > Wenn beim Initialisieren oder Verbinden Ihres Geräts nach dem Einspielen per Flashvorgang Probleme auftreten, helfen Ihnen die Informationen zu den weiteren Schritten unter „Problembehandlung“](troubleshoot-embedded-device-quickstarts.md) weiter.

1. Wählen Sie **Settings** aus.

1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port**: Der Porst, an dem Ihr Microchip E54 angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.
    * **Flusssteuerungsmodus**: DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie **OK** aus.

1. Drücken Sie an Ihrem Gerät die Taste **Reset**. Die Taste ist auf dem Gerät entsprechend beschriftet und befindet sich in der Nähe des Micro-USB-Anschlusses.

1. Überprüfen Sie in der App **Termite** die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

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

:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Microsoft Windows 10

* Hardware

  * Der [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker
  * Kabelgebundener Ethernet-Zugriff
  * Ethernet-Kabel

* [Termite.](https://www.compuphase.com/software_termite.htm) Wählen Sie auf der Webseite unter **Downloads and license** (Downloads und Lizenz) die Option für das vollständige Setup (complete setup) aus. Termite ist ein RS232-Terminal, mit dem Sie die Ausgabe für Ihr Gerät überwachen können.

* IAR Embedded Workbench for Arm (EW for Arm). Sie können eine [14-tägige kostenlose Testversion von IAR EW for Arm](https://www.iar.com/products/architectures/arm/iar-embedded-workbench-for-arm/) herunterladen und installieren.

* Laden Sie die Datei [Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2020_10_10.zip) herunter, und extrahieren Sie sie in einem Arbeitsverzeichnis. Wählen Sie ein Verzeichnis mit einem kurzen Pfad aus, um Compilerfehler bei der Erstellung zu vermeiden.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Wenn Sie den Mikrochip E54 mit Azure verbinden möchten, müssen Sie ihn mit Ihrem Computer verbinden, eine Konfigurationsdatei für die Azure IoT-Einstellungen ändern, das Image erstellen und es per Flashvorgang auf das Gerät übertragen.

### <a name="connect-the-device"></a>Verbinden des Geräts

1. Suchen Sie auf dem Microchip E54 die Schaltfläche **Zurücksetzen,** den **Ethernet**-Port und den Micro-USB-Port mit der Bezeichnung **Debug USB**. Jede der Komponenten ist in der folgenden Abbildung hervorgehoben:

    ![Suchen Sie die wichtigen Komponenten auf dem Microchip E54 Evaluation Kit Board](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Schließen Sie das Micro-USB-Kabel an den **Debug USB**-Port des Microchip E54 an and und verbinden Sie ihn dann mit Ihren Computer.

    > [!NOTE]
    > Weitere Informationen zum Einrichten und den ersten Schritte mit dem Microchip E54 finden Sie optional in dem [Benutzerhandbuch für SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf).

1. Verwenden Sie das Ethernet-Kabel, um den Microchip E54 mit einem Ethernet-Anschluss zu verbinden.

### <a name="configure-termite"></a>Konfigurieren von Termite

Mit der App **Termite** können Sie die Kommunikation überwachen und sich vergewissern, dass Ihr Gerät korrekt eingerichtet wurde. In diesem Abschnitt wird **Termite** für die Überwachung des seriellen Ports Ihres Geräts konfiguriert.

1. Starten Sie **Termite**.

1. Wählen Sie **Settings** aus.

1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port**: Der Porst, an dem Ihr Microchip E54 angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.
    * **Flusssteuerungsmodus**: DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie **OK** aus.

Von Termite können nun Ausgaben des Mikrochips E54 empfangen werden.

### <a name="configure-build-flash-and-run-the-image"></a>Konfigurieren, Erstellen, Flashen und Ausführen des Images

1. Öffnen Sie auf Ihrem Computer die App **IAR EW for Arm**.

1. Wählen Sie **File > Open workspace** („Datei“ > „Arbeitsbereich öffnen“) aus, navigieren Sie in dem Arbeitsordner, in dem Sie die ZIP-Datei extrahiert haben, zum Ordner **same54Xpro\iar**, und öffnen Sie den EWARM-Arbeitsbereich **_azure_rtos.eww_**.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-iar.png" alt-text="Öffnen des IAR-Arbeitsbereichs":::

1. Klicken Sie links unter **Workspace** (Arbeitsbereich) mit der rechten Maustaste auf das Projekt **sample_azure_iot_embedded_sdk_pnp**, und wählen Sie **Set as active** (Als aktiv festlegen) aus.

1. Erweitern Sie das Beispiel, erweitern Sie den Ordner **Sample**, und öffnen Sie anschließend die Datei „sample_config.h“.

1. Kommentieren Sie am Anfang der Datei die Direktive `#define ENABLE_DPS_SAMPLE` aus.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben. Die Konstante `ENDPOINT` wird auf den globalen Endpunkt für den Azure-Gerätebereitstellungsdienst (Device Provisioning Service, DPS) festgelegt.

    |Konstantenname|Wert|
    |-------------|-----|
    | `ENDPOINT` | global.azure-devices-provisioning.net |
    | `ID_SCOPE` | {*Ihr Wert für den ID-Bereich*} |
    | `REGISTRATION_ID` | {*Ihr Wert für die Geräte-ID*} |
    | `DEVICE_SYMMETRIC_KEY` | {*Ihr Wert für den Primärschlüssel*} |

    > [!NOTE]
    > Die Werte für `ENDPOINT`, `ID_SCOPE` und `REGISTRATION_ID` werden in einer Anweisung vom Typ `#ifndef ENABLE_DPS_SAMPLE` festgelegt. Achten Sie darauf, dass Sie die Werte in der Anweisung `#else` festlegen. Diese wird verwendet, wenn der Wert `ENABLE_DPS_SAMPLE` definiert wird.

1. Speichern Sie die Datei .

1. Wählen Sie **Project > Batch Build** („Projekt“ > „Batcherstellung“) aus. Wählen Sie anschließend **build_all** und **Make** (Erstellen) aus, um den Buildvorgang für alle Projekte durchzuführen. Im Bereich **Build** wird die Buildausgabe angezeigt. Vergewissern Sie sich, dass alle Beispielprojekte erfolgreich kompiliert und verknüpft wurden.

1. Wählen Sie auf der Symbolleiste die grüne Schaltfläche **Download and Debug** (Herunterladen und debuggen) aus, um das Programm herunterzuladen.

1. Wählen Sie nach dem Herunterladen des Images die Option **Go** (Los) aus, um das Beispiel auszuführen.

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Überprüfen Sie in der App **Termite** die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

:::zone-end
:::zone pivot="iot-toolset-mplab"

## <a name="prerequisites"></a>Voraussetzungen

* PC mit Microsoft Windows 10

* Hardware

  * Der [Microchip ATSAME54-XPro](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) (Microchip E54)
  * Kabel: USB 2.0 A-Stecker zu Micro-USB-Stecker
  * Kabelgebundener Ethernet-Zugriff
  * Ethernet-Kabel

* [Termite.](https://www.compuphase.com/software_termite.htm) Wählen Sie auf der Webseite unter **Downloads and license** (Downloads und Lizenz) die Option für das vollständige Setup (complete setup) aus. Termite ist ein RS232-Terminal, mit dem Sie die Ausgabe für Ihr Gerät überwachen können.

* [MPLAB X IDE 5.35](https://www.microchip.com/mplab/mplab-x-ide)

* [MPLAB XC32/32++ Compiler 2.4.0 oder höher](https://www.microchip.com/mplab/compilers)

* Laden Sie die Datei [Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2020_10_10.zip](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2020_10_10.zip) herunter, und entzippen Sie sie in einem Arbeitsverzeichnis. Wählen Sie ein Verzeichnis mit einem kurzen Pfad aus, um Compilerfehler bei der Erstellung zu vermeiden.

[!INCLUDE [iot-develop-embedded-create-central-app-with-device](../../includes/iot-develop-embedded-create-central-app-with-device.md)]

## <a name="prepare-the-device"></a>Vorbereiten des Geräts

Wenn Sie den Mikrochip E54 mit Azure verbinden möchten, müssen Sie ihn mit Ihrem Computer verbinden, eine Konfigurationsdatei für die Azure IoT-Einstellungen ändern, das Image erstellen und es per Flashvorgang auf das Gerät übertragen.

### <a name="connect-the-device"></a>Verbinden des Geräts

1. Suchen Sie auf dem Microchip E54 die Schaltfläche **Zurücksetzen,** den **Ethernet**-Port und den Micro-USB-Port mit der Bezeichnung **Debug USB**. Jede der Komponenten ist in der folgenden Abbildung hervorgehoben:

    ![Suchen Sie die wichtigen Komponenten auf dem Microchip E54 Evaluation Kit Board](media/quickstart-devkit-microchip-atsame54-xpro/microchip-xpro-board.png)

1. Schließen Sie das Micro-USB-Kabel an den **Debug USB**-Port des Microchip E54 an and und verbinden Sie ihn dann mit Ihren Computer.

    > [!NOTE]
    > Weitere Informationen zum Einrichten und den ersten Schritte mit dem Microchip E54 finden Sie optional in dem [Benutzerhandbuch für SAM E54 Xplained Pro](http://ww1.microchip.com/downloads/en/DeviceDoc/70005321A.pdf).

1. Verwenden Sie das Ethernet-Kabel, um den Microchip E54 mit einem Ethernet-Anschluss zu verbinden.

### <a name="configure-termite"></a>Konfigurieren von Termite

Mit der App **Termite** können Sie die Kommunikation überwachen und sich vergewissern, dass Ihr Gerät korrekt eingerichtet wurde. In diesem Abschnitt wird **Termite** für die Überwachung des seriellen Ports Ihres Geräts konfiguriert.

1. Starten Sie **Termite**.

1. Wählen Sie **Settings** aus.

1. Überprüfen Sie im Dialogfeld **Serial port settings** (Einstellungen für den seriellen Anschluss) die folgenden Einstellungen, und ändern Sie sie bei Bedarf:
    * **Baud rate** (Baudrate): 115.200
    * **Port**: Der Porst, an dem Ihr Microchip E54 angeschlossen ist. Falls die Dropdownliste mehrere Anschlussoptionen enthält, können Sie hier den richtigen Anschluss auswählen. Öffnen Sie den **Geräte-Manager** von Windows, und ermitteln Sie unter **Anschlüsse**, welcher Anschluss verwendet wird.
    * **Flusssteuerungsmodus**: DTR/DSR

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/termite-settings.png" alt-text="Screenshot: Einstellungen für serielle Anschlüsse in der Termite-App":::

1. Wählen Sie **OK** aus.

Von Termite können nun Ausgaben des Mikrochips E54 empfangen werden.

### <a name="configure-build-flash-and-run-the-image"></a>Konfigurieren, Erstellen, Flashen und Ausführen des Images

1. Öffnen Sie **MPLAB X IDE** auf Ihrem Computer.

1. Wählen Sie **File > Open project** („Datei“ > „Projekt öffnen“) aus. Navigieren Sie im Dialogfeld für die Projektöffnung zum Ordner **same54Xpro\mplab** (in dem Arbeitsordner, in dem Sie die ZIP-Datei extrahiert haben). Wählen Sie alle Projekte aus (nicht den Ordner **common_hardware_code** oder **docs**), und wählen Sie anschließend **Open Project** (Projekt öffnen) aus.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/open-project-mplab.png" alt-text="Öffnen der Projekte in der MPLab-IDE":::

1. Klicken Sie links unter **Projects** (Projekte) mit der rechten Maustaste auf das Projekt **sample_azure_iot_embedded_sdk_pnp**, und wählen Sie **Set as Main Project** (Als Hauptprojekt festlegen) aus.

1. Erweitern Sie das Projekt **sample_azure_iot_embedded_sdk_pnp**, erweitern Sie den Ordner **Header Files**, und öffnen Sie anschließend die Datei „sample_config.h“.

1. Kommentieren Sie am Anfang der Datei die Direktive `#define ENABLE_DPS_SAMPLE` aus.

    ```c
    #define ENABLE_DPS_SAMPLE
    ```

1. Legen Sie die Konstanten aus den Azure IoT-Geräteinformationen auf die Werte fest, die Sie nach dem Erstellen der Azure-Ressourcen gespeichert haben. Die Konstante `ENDPOINT` wird auf den globalen Endpunkt für den Azure-Gerätebereitstellungsdienst (Device Provisioning Service, DPS) festgelegt.

    |Konstantenname|Wert|
    |-------------|-----|
    | `ENDPOINT` | global.azure-devices-provisioning.net |
    | `ID_SCOPE` | {*Ihr Wert für den ID-Bereich*} |
    | `REGISTRATION_ID` | {*Ihr Wert für die Geräte-ID*} |
    | `DEVICE_SYMMETRIC_KEY` | {*Ihr Wert für den Primärschlüssel*} |

    > [!NOTE]
    > Die Werte für `ENDPOINT`, `ID_SCOPE` und `REGISTRATION_ID` werden in einer Anweisung vom Typ `#ifndef ENABLE_DPS_SAMPLE` festgelegt. Achten Sie darauf, dass Sie die Werte in der Anweisung `#else` festlegen. Diese wird verwendet, wenn der Wert `ENABLE_DPS_SAMPLE` definiert wird.

1. Speichern Sie die Datei .

1. Um das Beispiel erstellen zu können, müssen zunächst die abhängigen Bibliotheken des Projekts **sample_azure_iot_embedded_pnp** erstellt werden: **threadx**, **netxduo** und **same54_lib**. Klicken Sie zum Erstellen der einzelnen Bibliotheken jeweils im Bereich **Projects** (Projekte) mit der rechten Maustaste auf das zugehörige Projekt, und wählen Sie **Build** (Erstellen) aus. Warten Sie jeweils, bis der Buildvorgang abgeschlossen ist, bevor Sie mit der nächsten Bibliothek fortfahren.

1. Klicken Sie nach erfolgreicher Erstellung aller erforderlichen Bibliotheken mit der rechten Maustaste auf das Projekt **sample_azure_iot_embedded_pnp**, und wählen Sie **Build** (Erstellen) aus.

1. Wählen Sie im oberen Menü **Debug > Debug Main Project** („Debuggen“ > „Hauptprojekt debuggen) aus, um das Programm herunterzuladen und zu starten.

1. Sollte ein Dialogfeld vom Typ **Tool not Found** (Tool nicht gefunden) angezeigt werden, wählen Sie **connect SAM E54 board** (Verbindung mit SAM E54-Board herstellen) und anschließend **OK** aus.

1. Es kann einige Minuten dauern, bis das Programm heruntergeladen wurde und ausgeführt wird. Sobald das Programm erfolgreich heruntergeladen wurde und ausgeführt wird, wird im Bereich **Output** (Ausgabe) von MPLAB X IDE der folgende Status angezeigt:

    ```output
    Programming complete
    
    Running
    ```

### <a name="confirm-device-connection-details"></a>Bestätigen der Details zur Geräteverbindung

Überprüfen Sie in der App **Termite** die folgenden Prüfpunktwerte, um sich zu vergewissern, dass das Gerät initialisiert und mit Azure IoT verbunden wurde.

```output
DHCP In Progress...
IP address: 192.168.0.22
Mask: 255.255.255.0
Gateway: 192.168.0.1
DNS Server address: 75.75.75.75
SNTP Time Sync...
SNTP Time Sync successfully.
[INFO] Azure IoT Security Module has been enabled, status=0
Start Provisioning Client...
[INFO] IoTProvisioning client connect pending
Registered Device Successfully.
IoTHub Host Name: iotc-597ffb0b-3dbe-4784-ba3c-fdefd120a44a.azure-devices.net; Device ID: mydevice.
Connected to IoTHub.
Telemetry message send: {"temperature":22}.
Receive twin properties: {"desired":{"$version":1},"reported":{"maxTempSinceLastReboot":22,"$version":8}}
Failed to parse value
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
Telemetry message send: {"temperature":22}.
```

Lassen Sie Termite geöffnet, damit Sie die Geräteausgabe während der folgenden Schritte verfolgen können.

:::zone-end

## <a name="verify-the-device-status"></a>Überprüfen des Gerätestatus

Zeigen Sie den Gerätestatus im IoT Central-Portal wie folgt an:

:::zone pivot="iot-toolset-cmake"
1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Vergewissern Sie sich, dass der **Gerätestatus** auf *Bereitgestellt* aktualisiert wurde.
1. Vergewissern Sie sich, dass die Anzeige unter **Gerätevorlage** auf *Leitfaden für erste Schritte* aktualisiert wurde.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status.png" alt-text="Screenshot: Gerätestatus in IoT Central":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Vergewissern Sie sich, dass der **Gerätestatus** auf *Bereitgestellt* aktualisiert wurde.
1. Vergewissern Sie sich, dass die **Gerätevorlage** auf *Leitfaden Thermostat* aktualisiert wurde.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-view-status-iar.png" alt-text="Screenshot: Gerätestatus in IoT Central":::
:::zone-end

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Mit IoT Central können Sie den Weg der Telemetriedaten von Ihrem Gerät in die Cloud anzeigen.

Zeigen Sie die Telemetriedaten im IoT Central-Portal wie folgt an:

1. Wählen Sie im Anwendungsdashboard im seitlichen Navigationsmenü die Option **Geräte** aus.
1. Wählen Sie das Gerät in der Geräteliste aus.
1. Zeigen Sie die Telemetriedaten auf der Registerkarte **Übersicht** an, während das Gerät Nachrichten an die Cloud sendet.

    :::zone pivot="iot-toolset-cmake"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::
    :::zone-end
    :::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-telemetry-iar.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::
    :::zone-end

    > [!NOTE]
    > Sie können die Telemetriedaten auch auf dem Gerät überwachen, indem Sie die Termite-App verwenden.

## <a name="call-a-direct-method-on-the-device"></a>Aufrufen einer direkten Methode auf dem Gerät

Darüber hinaus haben Sie auch die Möglichkeit, IoT Central zum Aufrufen einer direkten Methode zu verwenden, die Sie auf Ihrem Gerät implementiert haben. Direkte Methoden haben einen Namen und können optional über JSON-Nutzdaten, eine konfigurierbare Verbindung und ein Methodentimeout verfügen. In diesem Abschnitt rufen Sie eine Methode auf, mit der Sie eine LED ein- oder ausschalten können.

Rufen Sie eine Methode wie folgt im IoT Central-Portal auf:
:::zone pivot="iot-toolset-cmake"

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehl** aus.

1. Wählen Sie in der Dropdownliste **Status** die Option **True** und dann **Ausführen** aus.  Die LED sollte aufleuchten.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Wählen Sie in der Dropdownliste **Status** die Option **False** und dann **Ausführen** aus. Die LED sollte nicht mehr aufleuchten.
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"

1. Wählen Sie auf der Geräteseite die Registerkarte **Befehl** aus.

1. Legen Sie im Feld **Since** (Seit) mithilfe der Datums- und Uhrzeitauswahl eine Zeit fest, und wählen Sie anschließend **Run** (Ausführen) aus.

    :::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-invoke-method-iar.png" alt-text="Screenshot: Aufrufen einer direkten Methode auf einem Gerät in IoT Central":::

1. Der Befehlsaufruf wird in Termite angezeigt:

    ```output
    Receive method call: getMaxMinReport, with payload:"2021-10-14T17:45:00.000Z"
    ```

    > [!NOTE]
    > Sie können sich den Befehlsaufruf und die Antwort aber auch auf der Registerkarte **Rohdaten** der Geräteseite in IoT Central ansehen.
:::zone-end

## <a name="view-device-information"></a>Anzeigen von Geräteinformationen

Sie können die Geräteinformationen in IoT Central anzeigen.

Wählen Sie auf der Geräteseite die Registerkarte **Info** aus.

:::zone pivot="iot-toolset-cmake"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm, iot-toolset-mplab"
:::image type="content" source="media/quickstart-devkit-microchip-atsame54-xpro/iot-central-device-about-iar.png" alt-text="Screenshot: Geräteinformationen in IoT Central":::
:::zone-end

## <a name="troubleshoot-and-debug"></a>Problembehandlung und Debuggen

Wenn beim Erstellen des Gerätecodes, beim Einspielen per Flashvorgang auf dem Gerät oder beim Herstellen einer Verbindung Probleme auftreten, lesen Sie [Problembehandlung](troubleshoot-embedded-device-quickstarts.md).

:::zone pivot="iot-toolset-cmake"
Informationen zum Debuggen der Anwendung finden Sie unter [Debuggen mit Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).
:::zone-end
:::zone pivot="iot-toolset-iar-ewarm"
Hilfreiche Informationen zum Debuggen der Anwendung finden Sie in **IAR EW for Arm** in den Auswahloptionen unter **Help** (Hilfe).  
:::zone-end
:::zone pivot="iot-toolset-mplab"
Hilfreiche Informationen zum Debuggen der Anwendung finden Sie in **MPLAB X IDE** in den Auswahloptionen unter **Help** (Hilfe).  
:::zone-end

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
