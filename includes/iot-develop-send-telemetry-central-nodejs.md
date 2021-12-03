---
title: include file
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 09/17/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 23970d887c1e3d2523c69ae0ace25fdc7b0094ff
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860464"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Zunächst erstellen Sie eine Azure IoT Central-Anwendung zum Hosten von Geräten. Anschließend verwenden Sie ein SDK-Beispiel für ein Azure IoT-Gerät, um einen Temperaturregler zu erstellen, ihn sicher mit IoT Central zu verbinden und Telemetriedaten zu senden. Die Beispielanwendung für den Temperaturregler wird auf Ihrem lokalen Computer ausgeführt und generiert simulierte Sensordaten, die an IoT Central gesendet werden sollen.

## <a name="prerequisites"></a>Voraussetzungen
Diese Schnellstartanleitung wird unter Windows, Linux und Raspberry Pi ausgeführt. Sie wurde mit den folgenden Betriebssystem- und Geräteversionen getestet:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS Version 10 (Buster), ausgeführt auf einem Raspberry Pi 3 Model B+

Installieren Sie die folgenden Komponenten auf dem Entwicklungscomputer:

- [Node.js](https://nodejs.org/)-Version 6 oder höher. Führen Sie zum Überprüfen Ihrer Version `node --version` in Ihrer Konsolen-App aus.
- [Git](https://git-scm.com/downloads).

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels
In diesem Abschnitt konfigurieren Sie Ihre lokale Umgebung, installieren das Azure IoT-Node.js-Geräte-SDK und führen ein Beispiel aus, mit dem ein Temperaturregler erstellt wird.

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

1. Kopieren Sie das Azure IoT-Node.js-Geräte-SDK auf Ihren lokalen Computer.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-node
    ```

1. Navigieren Sie zum Beispielverzeichnis.

    **Windows**
    ```console
    cd azure-iot-sdk-node\device\samples\javascript\pnp
    ```

    **Linux oder Raspberry Pi OS**
    ```console
    cd azure-iot-sdk-node/device/samples/javascript/pnp
    ```

1. Installieren Sie das Node.js SDK von Azure IoT sowie die erforderlichen Abhängigkeiten:
    ```console
    npm install
    ```

### <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie in Ihrer Konsole das folgende Codebeispiel aus dem SDK aus. Im Beispiel wird ein Temperaturregler mit Thermostatsensoren erstellt.
    ```console
    node pnpTemperatureController.js
    ```

    Nachdem Ihr Gerät eine Verbindung mit der IoT Central-Anwendung hergestellt hat, stellt es eine Verbindung mit der Geräteinstanz her, die Sie in der Anwendung erstellt haben, und beginnt mit dem Senden von Telemetriedaten. Die Verbindungsdetails und die Telemetrieausgabe werden in der Konsole angezeigt: 
    
    ```output
    registration succeeded
    assigned hub=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net
    deviceId=my-sdk-device
    payload=undefined
    Connecting using connection string: HostName=iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx.azure-devices.net;DeviceId=my-sdk-device;SharedAccessKey=xxxxxxxxxxxxxxxxxxxxxxxxxxx
    Enabling the commands on the client
    Please enter q or Q to exit sample.
    The following properties will be updated for root interface.
    { serialNumber: 'alwinexlepaho8329' }
    The following properties will be updated for component: thermostat1
    {
      thermostat1: { maxTempSinceLastReboot: 40.53506261527863, __t: 'c' }
    }
    The following properties will be updated for component: thermostat2
    {
      thermostat2: { maxTempSinceLastReboot: 89.55136974144273, __t: 'c' }
    }
    The following properties will be updated for component: deviceInformation
    {
      deviceInformation: {
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
        __t: 'c'
      }
    }
    executed sample
    Received an update for device with value: {"$version":1}
    Properties have been reported for root interface.
    Properties have been reported for component: thermostat1
    Properties have been reported for component: thermostat2
    Properties have been reported for component: deviceInformation
    Sending telemetry message 0 from component: thermostat1
    Sending telemetry message 0 from component: thermostat2
    ```