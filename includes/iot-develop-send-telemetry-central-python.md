---
title: include file
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: e5d83a17edd2cff6c58ee7ac646c2bdf2fe30f5e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860915"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Zunächst erstellen Sie eine Azure IoT Central-Anwendung zum Hosten von Geräten. Anschließend verwenden Sie ein SDK-Beispiel für ein Azure IoT-Gerät, um einen Temperaturregler zu erstellen, ihn sicher mit IoT Central zu verbinden und Telemetriedaten zu senden. Die Beispielanwendung für den Temperaturregler wird auf Ihrem lokalen Computer ausgeführt und generiert simulierte Sensordaten, die an IoT Central gesendet werden sollen.

## <a name="prerequisites"></a>Voraussetzungen
Diese Schnellstartanleitung wird unter Windows, Linux und Raspberry Pi ausgeführt. Sie wurde mit den folgenden Betriebssystem- und Geräteversionen getestet:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS Version 10 (Buster), ausgeführt auf einem Raspberry Pi 3 Model B+

Installieren Sie die folgenden Komponenten auf dem Entwicklungscomputer:

- [Python](https://www.python.org/downloads/)-Version 3.7 oder höher. Führen Sie zum Überprüfen Ihrer Python-Version `python3 --version` aus.
- [Git](https://git-scm.com/downloads).

[!INCLUDE [iot-develop-create-central-app-with-device](iot-develop-create-central-app-with-device.md)]

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels
In diesem Abschnitt konfigurieren Sie Ihre lokale Umgebung, installieren das Azure IoT-Python-Geräte-SDK und führen ein Beispiel aus, mit dem ein Temperaturregler erstellt wird.

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

1. Kopieren Sie das Azure IoT-Python-Geräte-SDK auf Ihren lokalen Computer.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. Navigieren Sie zum Beispielverzeichnis.

    **Windows**
    ```console
    cd azure-iot-sdk-python\azure-iot-device\samples\pnp
    ```

    **Linux oder Raspberry Pi OS**
    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```

1. Installieren Sie das Azure IoT Python SDK.
    ```console
    pip3 install azure-iot-device
    ```

### <a name="run-the-code"></a>Ausführen des Codes

1. Führen Sie in Ihrer Konsole das folgende Codebeispiel aus dem SDK aus. Im Beispiel wird ein Temperaturregler mit Thermostatsensoren erstellt.
    ```console
    python3 temp_controller_with_thermostats.py
    ```

    Nachdem Ihr Gerät eine Verbindung mit der IoT Central-Anwendung hergestellt hat, stellt es eine Verbindung mit der Geräteinstanz her, die Sie in der Anwendung erstellt haben, und beginnt mit dem Senden von Telemetriedaten. Die Verbindungsdetails und die Telemetrieausgabe werden in der Konsole angezeigt: 
    
    ```output
    Device was assigned
    iotc-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azure-devices.net
    my-sdk-device
    Updating pnp properties for root interface
    {'serialNumber': 'alohomora'}
    Updating pnp properties for thermostat1
    {'thermostat1': {'maxTempSinceLastReboot': 98.34, '__t': 'c'}}
    Updating pnp properties for thermostat2
    {'thermostat2': {'maxTempSinceLastReboot': 48.92, '__t': 'c'}}
    Updating pnp properties for deviceInformation
    {'deviceInformation': {'swVersion': '5.5', 'manufacturer': 'Contoso Device Corporation', 'model': 'Contoso 4762B-turbo', 'osName': 'Mac Os', 'processorArchitecture': 'x86-64', 'processorManufacturer': 'Intel', 'totalStorage': 1024, 'totalMemory': 32, '__t': 'c'}}
    Listening for command requests and property updates
    Press Q to quit
    Sending telemetry from various components
    Sent message
    {"temperature": 33}
    ```