---
title: Datei einfügen
description: Datei einfügen
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 08/03/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: 086370583f77ddadeae156fe0fa3d5babf80d815
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129516624"
---
[![Code durchsuchen](../articles/iot-develop/media/common/browse-code.svg)](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples)

In dieser Schnellstartanleitung lernen Sie einen einfachen Entwicklungsworkflow für Azure IoT-Anwendungen kennen. Sie verwenden die Azure CLI und den loT Explorer, um einen Azure loT-Hub und ein Gerät zu erstellen. Anschließend verwenden Sie ein Azure IoT-Geräte-SDK-Beispiel, um einen simulierten Temperaturregler auszuführen, ihn sicher mit dem Hub zu verbinden und Telemetriedaten zu senden.

## <a name="prerequisites"></a>Voraussetzungen
- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
- [Visual Studio (Community, Professional oder Enterprise) 2019](https://visualstudio.microsoft.com/downloads/)
- Eine lokale Kopie des GitHub-Repositorys mit [Microsoft Azure IoT-Beispielen für C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp). Laden Sie eine Kopie des Repositorys herunter, und extrahieren Sie es: [Zip herunterladen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)
- [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases): Plattformübergreifendes Hilfsprogramm mit grafischer Benutzeroberfläche zum Überwachen und Verwalten von Azure IoT. 
- Azure-Befehlszeilenschnittstelle. In dieser Schnellstartanleitung gibt es zwei Möglichkeiten zum Ausführen von Azure CLI-Befehlen:
    - Verwenden Sie Azure Cloud Shell. Dabei handelt es sich um eine interaktive Shell, mit der CLI-Befehle im Browser ausgeführt werden. Diese Option wird empfohlen, da Sie nichts installieren müssen. Wenn Sie Cloud Shell zum ersten Mal verwenden, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Führen Sie in der [Cloud Shell-Schnellstartanleitung](../articles/cloud-shell/quickstart.md) die Schritte zum **Starten von Cloud Shell** und **Auswählen der Bash-Umgebung** aus.
    - Führen Sie optional die Azure CLI auf dem lokalen Computer aus. Wenn die Azure CLI bereits installiert ist, führen Sie `az upgrade` aus, um die CLI und die Erweiterungen auf die aktuelle Version zu aktualisieren. Informationen zur Installation der Azure CLI finden Sie unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [iot-hub-include-create-hub-iot-explorer](iot-hub-include-create-hub-iot-explorer.md)]

## <a name="run-a-simulated-device"></a>Ausführen eines simulierten Geräts
In diesem Abschnitt verwenden Sie das C# SDK zum Senden von Nachrichten von einem simulierten Gerät an den IoT-Hub. Sie führen ein Beispiel aus, das einen Temperaturregler mit zwei Thermostatsensoren implementiert.

So führen Sie die Beispielanwendung in Visual Studio aus:

1. Öffnen Sie in dem Order, in dem Sie die Azure IoT-Beispiele für C# extrahiert haben, die Projektmappendatei *azure-iot-samples-csharp-main\iot-hub\Samples\device\IoTHubDeviceSamples.sln* in Visual Studio. 

1. Wählen Sie im **Projektmappen-Explorer** die Projektdatei **PnpDeviceSamples > TemperatureController** aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Als Startprojekt festlegen** aus.

1. Klicken Sie mit der rechten Maustaste auf das Projekt **TemperatureController**, wählen Sie **Eigenschaften** und dann die Registerkarte **Debuggen** aus, und fügen Sie dem Projekt die folgenden Umgebungsvariablen hinzu:

    | Name | Wert |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | *connectionString* |
    | IOTHUB_DEVICE_CONNECTION_STRING | Die Verbindungszeichenfolge, die Sie zuvor gespeichert haben |

1. Speichern Sie die aktualisierte Projektdatei **TemperatureController**.

1. Drücken Sie in Visual Studio STRG+F5, um das Beispiel auszuführen.

Dann wird ein Konsolenfenster geöffnet. Das Beispiel stellt eine sichere Verbindung mit Ihrem IoT-Hub als das Gerät her, das Sie registriert haben, und beginnt mit dem Senden von Telemetrienachrichten. Die Beispielausgabe wird in der Konsole angezeigt.

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

1. Führen Sie den Befehl [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) aus, um Ereignisse zu überwachen, die von dem simulierten Gerät an Ihren IoT-Hub gesendet werden. Verwenden Sie die Namen, die Sie zuvor in Azure IoT für Ihr Gerät und Ihren IoT-Hub erstellt haben.

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
