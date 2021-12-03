---
title: include file
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 10/07/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 14b6d365b38181ffd6fb3a9b3d8cf72abd85cfa4
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861321"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Sie verwenden die Azure CLI und den loT Explorer, um einen Azure loT-Hub und ein Gerät zu erstellen. Anschließend verwenden Sie ein Azure IoT-Geräte-SDK-Beispiel, um einen Temperaturregler auszuführen, ihn sicher mit dem Hub zu verbinden und Telemetriedaten zu senden. Die Beispielanwendung für den Temperaturcontroller wird auf Ihrem lokalen Computer ausgeführt und generiert simulierte Sensordaten, die an IoT Hub gesendet werden sollen.

## <a name="prerequisites"></a>Voraussetzungen

Diese Schnellstartanleitung wird unter Windows, Linux und Raspberry Pi ausgeführt. Sie wurde mit den folgenden Betriebssystem- und Geräteversionen getestet:

- Windows 10
- Ubuntu 20.04 LTS
- Raspberry Pi OS Version 10 (Raspian), das auf einem Raspberry Pi 3 Model B+ ausgeführt wird

Installieren Sie die folgenden Voraussetzungen auf Ihrem Entwicklungscomputer – es sei denn, dass dies für Raspberry Pi angegeben ist:

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
- [Git](https://git-scm.com/downloads).
- .NET Core SDK 3.1. Installieren Sie unbedingt das .NET SDK, nicht nur die Runtime. Führen Sie `dotnet --info` aus, um die Version des .NET SDK und der Runtime zu überprüfen, die auf Ihrem Computer installiert sind.

  - Befolgen Sie unter Windows und Linux (mit Ausnahme von Raspberry Pi) die Anweisungen zum [Installieren des .NET Core SDK 3.1](/dotnet/core/install/) auf Ihrer Plattform.
  - Für Raspberry Pi müssen Sie die Anweisungen für die [manuelle Installation des SDK](/dotnet/core/install/linux-scripted-manual#manual-install) ausführen. Dies liegt daran, dass unter Debian Paket-Manager-Installationen des .NET SDK nur für die x64-Architektur unterstützt werden.

- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Plattformübergreifendes Hilfsprogramm mit grafischer Benutzeroberfläche zum Überwachen und Verwalten von Azure IoT. Wenn Sie Raspberry Pi als Ihre Entwicklungsplattform verwenden, empfehlen wir, IoT Explorer auf einem anderen Computer zu installieren. Wenn Sie IoT Explorer nicht installieren möchten, können Sie mithilfe der Azure CLI die gleichen Schritte ausführen. 
- Azure-Befehlszeilenschnittstelle. In dieser Schnellstartanleitung gibt es zwei Möglichkeiten zum Ausführen von Azure CLI-Befehlen:
    - Verwenden Sie Azure Cloud Shell. Dabei handelt es sich um eine interaktive Shell, mit der CLI-Befehle im Browser ausgeführt werden. Diese Option wird empfohlen, da Sie nichts installieren müssen. Wenn Sie Cloud Shell zum ersten Mal verwenden, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Führen Sie in der [Cloud Shell-Schnellstartanleitung](../articles/cloud-shell/quickstart.md) die Schritte zum **Starten von Cloud Shell** und **Auswählen der Bash-Umgebung** aus.
    - Führen Sie optional die Azure CLI auf dem lokalen Computer aus. Wenn die Azure CLI bereits installiert ist, führen Sie `az upgrade` aus, um die CLI und die Erweiterungen auf die aktuelle Version zu aktualisieren. Informationen zur Installation der Azure CLI finden Sie unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). Wenn Sie Raspberry Pi als Entwicklungsplattform verwenden, empfehlen wir Ihnen, Azure Cloud Shell zu verwenden oder Azure CLI auf einem anderen Computer zu installieren.

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-the-device-sample"></a>Ausführen des Gerätebeispiels

In diesem Abschnitt verwenden Sie das C# SDK zum Senden von Nachrichten von einem Gerät an Ihren IoT-Hub. Sie führen ein Beispiel aus, das einen Temperaturregler mit zwei Thermostatsensoren implementiert.

1. Öffnen Sie eine neue Konsole wie Windows CMD: PowerShell oder Bash. In den folgenden Schritten werden Sie diese Konsole verwenden, um das Node.js SDK zu installieren und mit Node.js-Beispielcode zu arbeiten.

    > [!NOTE]
    > Wenn Sie eine lokale Installation der Azure CLI verwenden, sind nun möglicherweise zwei Konsolenfenster geöffnet. Achten Sie darauf, dass Sie die Befehle in diesem Abschnitt in der soeben geöffneten Konsole eingeben, nicht in der Konsole, die Sie für die CLI verwendet haben.

1. Klonen Sie die [Microsoft Azure IoT-Beispiele für C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) auf Ihrem lokalen Computer:

    ```console
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Navigieren Sie zum Beispielverzeichnis:

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

1. Legen Sie die beiden folgenden Umgebungsvariablen fest, damit Ihr Gerät eine Verbindung mit Azure IoT herstellen kann:
    * Legen Sie eine Umgebungsvariable mit dem Namen `IOTHUB_DEVICE_CONNECTION_STRING` fest. Verwenden Sie als Variablenwert die Geräteverbindungszeichenfolge, die Sie im vorherigen Abschnitt gespeichert haben.
    * Legen Sie eine Umgebungsvariable mit dem Namen `IOTHUB_DEVICE_SECURITY_TYPE` fest. Verwenden Sie als Variable den Literalzeichenfolgenwert `connectionString`.

    **CMD (Windows)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > In den Windows-CMD-Befehlen sind die Zeichenfolgenwerte nicht in Anführungszeichen eingeschlossen.

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```
1. Führen Sie das Codebeispiel aus:

    ```console
    dotnet run
    ```
    > [!NOTE]
    > In diesem Codebeispiel wird Azure IoT Plug & Play verwendet. Dadurch wird die Integration intelligenter Geräte in Ihre Lösungen ohne manuelle Konfiguration ermöglicht.  In den meisten Beispielen in dieser Dokumentation wird standardmäßig IoT Plug & Play verwendet. Weitere Informationen zu den Vorteilen und Einsatzmöglichkeiten von IoT Plug & Play finden Sie unter [Was ist IoT Plug & Play?](../articles/iot-develop/overview-iot-plug-and-play.md).

Das Beispiel stellt eine sichere Verbindung mit Ihrem IoT-Hub als das Gerät her, das Sie registriert haben, und beginnt mit dem Senden von Telemetrienachrichten. Die Beispielausgabe wird in der Konsole angezeigt.

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten

Sie können die Gerätetelemetriedaten mit IoT Explorer anzeigen. Optional können Sie Telemetriedaten mithilfe der Azure CLI anzeigen.

So zeigen Sie Telemetriedaten in Azure IoT Explorer an:

1. Wählen Sie in IoT Explorer für Ihren IoT-Hub die Option **Geräte in diesem Hub anzeigen** und dann in der Liste Ihr Gerät aus. 
1. Wählen Sie im Menü auf der linken Seite für Ihr Gerät die Option **Telemetrie** aus.
1. Vergewissern Sie sich, dass **Use built-in event hub** (Integrierten Event Hub verwenden) auf *Ja* festgelegt ist, und wählen Sie dann **Starten** aus.
1. Zeigen Sie die Telemetriedaten an, während das Gerät Nachrichten in die Cloud sendet.

    :::image type="content" source="media/iot-develop-send-telemetry-iot-hub-csharp/iot-explorer-device-telemetry.png" alt-text="Screenshot: Gerätetelemetrie in IoT Central":::

1. Wählen Sie **Beenden** aus, um den Empfang von Ereignissen zu beenden.

Zum Lesen von Telemetriedaten, die von einzelnen Gerätekomponenten gesendet werden, können Sie die Plug & Play-Funktionen in IoT Explorer verwenden. Der Temperaturregler in dieser Schnellstartanleitung verfügt beispielsweise über zwei Thermostate: thermostat1 und thermostat2. So zeigen Sie die von thermostat1 gemeldete Temperatur an: 

1. Wählen Sie auf Ihrem Gerät in IoT Explorer im Menü auf der linken Seite **IoT Plug & Play-Komponenten** aus. Wählen Sie dann in der Liste der Komponenten **thermostat1** aus.

1. Wählen Sie im Bereich für die Komponente **thermostat1** im oberen Menü **Telemetrie** aus.

1. Führen Sie im Bereich **Telemetrie** die gleichen Schritte wie zuvor aus. Stellen Sie sicher, dass **Use built-in event hub** (Integrierten Event Hub verwenden) auf *Ja* festgelegt ist, und wählen Sie dann **Starten** aus.

So zeigen Sie Gerätetelemetriedaten mit der Azure CLI an:

1. Führen Sie den Befehl [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) zum Überwachen von Ereignissen aus, die vom Gerät an Ihren IoT-Hub gesendet werden. Verwenden Sie die Namen, die Sie zuvor in Azure IoT für Ihr Gerät und Ihren IoT-Hub erstellt haben.

    ```azurecli
    az iot hub monitor-events --output table --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Zeigen Sie die Verbindungsdetails und die Telemetrieausgabe in der Konsole an.

    ```output
    Starting event monitor, filtering on device: mydevice, use ctrl-c to stop...
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 39.8
    
    event:
      component: thermostat2
      interface: dtmi:com:example:TemperatureController;2
      module: ''
      origin: mydevice
      payload:
        temperature: 36.7
    ```

1. Wählen Sie STRG+C aus, um die Überwachung zu beenden.
