---
title: include file
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/08/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 1a61f369d0987caac2cc821e37c1e5e1ed7cda40
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860483"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Zunächst erstellen Sie eine Azure IoT Central-Anwendung zum Hosten von Geräten. Anschließend verwenden Sie ein SDK-Beispiel für ein Azure IoT-Gerät, um einen Temperaturregler zu erstellen, ihn sicher mit IoT Central zu verbinden und Telemetriedaten zu senden. Die Beispielanwendung für den Temperaturregler wird auf Ihrem lokalen Computer ausgeführt und generiert simulierte Sensordaten, die an IoT Central gesendet werden sollen.

## <a name="prerequisites"></a>Voraussetzungen

Diese Schnellstartanleitung wird unter Windows, Linux und Raspberry Pi ausgeführt. Sie wurde mit den folgenden Betriebssystem- und Geräteversionen getestet:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS Version 10 (Raspian), das auf einem Raspberry Pi 3 Model B+ ausgeführt wird

Installieren Sie die folgenden Komponenten auf dem Entwicklungscomputer:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
- [Git](https://git-scm.com/downloads).
- .NET Core SDK 3.1. Installieren Sie unbedingt das .NET SDK, nicht nur die Runtime. Führen Sie `dotnet --info` aus, um die Version des .NET SDK und der Runtime zu überprüfen, die auf Ihrem Computer installiert sind.

  - Befolgen Sie unter Windows und Linux (mit Ausnahme von Raspberry Pi) die Anweisungen zum [Installieren des .NET Core SDK 3.1](/dotnet/core/install/) auf Ihrer Plattform.
  - Für Raspberry Pi müssen Sie die Anweisungen für die [manuelle Installation des SDK](/dotnet/core/install/linux-scripted-manual#manual-install) ausführen. Dies liegt daran, dass unter Debian Paket-Manager-Installationen des .NET SDK nur für die x64-Architektur unterstützt werden.

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels
In diesem Abschnitt konfigurieren Sie Ihre lokale Umgebung, installieren die Azure IoT C#-Beispiele und führen ein Beispiel aus, das einen Temperaturregler erstellt.

### <a name="configure-your-environment"></a>Konfigurieren Ihrer Umgebung

1. Öffnen Sie eine Konsole, etwa die Windows-Eingabeaufforderung, PowerShell oder Bash.

1. Legen Sie mit den entsprechenden Befehle für Ihre Konsole die folgenden Umgebungsvariablen fest. Das Gerät verwendet diese Werte, um eine Verbindung mit IoT Central herzustellen. Verwenden Sie für `IOTHUB_DEVICE_DPS_ID_SCOPE`, `IOTHUB_DEVICE_DPS_DEVICE_KEY` und `IOTHUB_DEVICE_DPS_DEVICE_ID` die Geräteverbindungswerte, die Sie zuvor gespeichert haben.

    **Eingabeaufforderung (Windows)**

    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=DPS
    set IOTHUB_DEVICE_DPS_ID_SCOPE=<application ID scope>
    set IOTHUB_DEVICE_DPS_DEVICE_KEY=<device primary key>
    set IOTHUB_DEVICE_DPS_DEVICE_ID=<your device ID>
    set IOTHUB_DEVICE_DPS_ENDPOINT=global.azure-devices-provisioning.net
    ```

    > [!NOTE]
    > In den Windows-CMD-Befehlen sind die Variablenwerte nicht in Anführungszeichen eingeschlossen.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    $env:IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    $env:IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    $env:IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE='DPS'
    export IOTHUB_DEVICE_DPS_ID_SCOPE='<application ID scope>'
    export IOTHUB_DEVICE_DPS_DEVICE_KEY='<device primary key>'
    export IOTHUB_DEVICE_DPS_DEVICE_ID='<your device ID>'
    export IOTHUB_DEVICE_DPS_ENDPOINT='global.azure-devices-provisioning.net' 
    ```

### <a name="install-the-sdk-and-samples"></a>Installieren des SDK und der Beispiele

1. Klonen Sie die [Microsoft Azure IoT-Beispiele für C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) auf Ihrem lokalen Computer.

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Navigieren Sie zum Beispielverzeichnis.

    **Windows**

    ```console
    cd azure-iot-samples-csharp\iot-hub\Samples\device\PnpDeviceSamples\TemperatureController
    ```

    **Linux oder Raspberry Pi OS**

    ```console
    cd azure-iot-samples-csharp/iot-hub/Samples/device/PnpDeviceSamples/TemperatureController
    ```

1. Installieren Sie das C# SDK von Azure IoT sowie die erforderlichen Abhängigkeiten:

    ```console
    dotnet restore
    ```

    Mit diesem Befehl werden die richtigen Abhängigkeiten installiert, die in der Datei *TemperatureController.csproj* angegeben sind.

### <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie in Ihrer Konsole das Codebeispiel aus. Das Beispiel erstellt einen Temperaturregler mit Thermostatsensoren.

    ```console
    dotnet run
    ```

    Nachdem Ihr Gerät eine Verbindung mit der IoT Central-Anwendung hergestellt hat, stellt es eine Verbindung mit der Geräteinstanz her, die Sie in der Anwendung erstellt haben, und beginnt mit dem Senden von Telemetriedaten. Die Verbindungsdetails und die Telemetrieausgabe werden in der Konsole angezeigt:

    ```output
    [10/09/2021 00:29:18]info: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Press Control+C to quit the sample.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set up the device client.
    [10/09/2021 00:29:18]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Initializing via DPS
    [10/09/2021 00:29:38]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for 'reboot' command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Connection status change registered - status=Connected, reason=Connection_Ok.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler for "getMaxMinReport" command.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Set handler to receive 'targetTemperature' updates.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component = 'deviceInformation', properties update is complete.
    [10/09/2021 00:29:39]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - { "serialNumber": "SR-123456" } is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat1", { "temperature": 23.7 } in °C.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Property: Update - component="thermostat1", { "maxTempSinceLastReboot": 23.7 } in °C is complete.
    [10/09/2021 00:29:40]dbug: Microsoft.Azure.Devices.Client.Samples.TemperatureControllerSample[0]
          Telemetry: Sent - component="thermostat2", { "temperature": 25.8 } in °C.
    ```