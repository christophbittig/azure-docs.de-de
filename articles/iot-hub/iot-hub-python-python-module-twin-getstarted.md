---
title: Modulidentität und Modulzwilling von Azure IoT Hub (Python)
description: Hier erfahren Sie, wie Sie mit IoT-SDKs für Python eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: lizross
ms.custom: devx-track-python
ms.openlocfilehash: 47570d0e3557cda3c5781edd046e69d04ba4cb9d
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553418"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts. Außerdem geben sie Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität hingegen für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen sie isolierte Konfigurationen und Zustände für die einzelnen Komponenten.
>

Am Ende dieses Tutorials verfügen Sie über drei Python-Apps:

* **CreateModule**: Hiermit werden eine Geräteidentität, eine Modulidentität und zugehörige Sicherheitsschlüssel zum Verbinden Ihrer Geräte- und Modulclients erstellt.

* **UpdateModuleTwinReportedProperties**: Hiermit werden aktualisierte gewünschte Eigenschaften für den Modulzwilling an Ihren IoT Hub gesendet.

* **ReceiveModuleTwinDesiredPropertiesPatch**: Empfangen des Patchs mit den gewünschten Eigenschaften für den Modulzwilling auf Ihrem Gerät.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, der ein Gerät in der Identitätsregistrierung hinzufügt und dann diesem Gerät ein Modul. Dieser Dienst erfordert die Berechtigung **Schreibvorgänge in Registrierung** (die auch **Lesevorgänge in Registrierung** umfasst). Außerdem erstellen Sie einen Dienst, der dem Modulzwilling für das neu erstellte Modul gewünschte Eigenschaften hinzufügt. Dieser Dienst erfordert die Berechtigung **Dienstverbindung**. Obwohl es standardmäßige SAS-Richtlinien gibt, mit denen diese Berechtigungen einzeln erteilt werden, erstellen Sie in diesem Abschnitt eine benutzerdefinierte SAS-Richtlinie, die beide Berechtigungen enthält.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Erstellen einer Geräteidentität und einer Modulidentität in IoT Hub

In diesem Abschnitt erstellen Sie eine Python-Dienst-App, mit der eine Geräte- und eine Modulidentität in der Identitätsregistrierung Ihrer IoT Hub-Identität erstellt werden. Ein Gerät oder Modul kann nur eine Verbindung mit IoT Hub herstellen, wenn in der Identitätsregistrierung ein Eintrag für dieses Gerät vorhanden ist. Weitere Informationen finden Sie unter [Grundlegendes zur Identitätsregistrierung in Ihrer IoT Hub-Instanz](iot-hub-devguide-identity-registry.md). Wenn Sie diese Konsolen-App ausführen, generiert sie eine eindeutige ID und einen eindeutigen Schlüssel für das Gerät und das Modul. Ihr Gerät und Ihr Modul verwenden diese Werte, um sich beim Senden von D2C-Nachrichten an IoT Hub zu identifizieren. Bei den IDs gilt Groß-/Kleinschreibung.

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-hub** zu installieren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **msrest** zu installieren. Sie benötigen dieses Paket, um **HTTPOperationError**-Ausnahmen abzufangen.

    ```cmd/sh
    pip install msrest
    ```

1. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsverzeichnis eine Datei mit dem Namen **CreateModule.py**.

1. Fügen Sie der Python-Datei den folgenden Code hinzu. Ersetzen Sie *YourIoTHubConnectionString* durch die Verbindungszeichenfolge, die Sie unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus:

    ```cmd/sh
    python CreateModule.py
    ```

Diese App erstellt eine Geräteidentität mit der ID **myFirstDevice** und ein Identitätsmodul mit der ID **myFirstModule** unter dem Gerät **myFirstDevice**. (Falls die Geräte- oder Modul-ID in der Identitätsregistrierung bereits vorhanden ist, werden mit dem Code lediglich die vorhandenen Geräte- bzw. Modulinformationen abgerufen.) Die App zeigt die ID und den Primärschlüssel für jede Identität an.

> [!NOTE]
> Die Identitätsregistrierung in IoT Hub speichert nur Geräte- und Modulidentitäten, um einen sicheren Zugriff auf IoT Hub zu ermöglichen. In der Identitätsregistrierung werden Geräte-IDs und -schlüssel für die Verwendung als Sicherheitsanmeldeinformationen gespeichert. Darüber hinaus wird in der Identitätsregistrierung ein Flag für den Aktivierungszustand des jeweiligen Geräts gespeichert, mit dem Sie den Zugriff für das betreffende Gerät deaktivieren können. Wenn Ihre Anwendung das Speichern weiterer gerätespezifischer Metadaten erfordert, sollte dafür ein anwendungsspezifischer Speicher verwendet werden. Es gibt keinen Flag „Aktiviert/deaktiviert“ für Modulidentitäten. Weitere Informationen finden Sie unter [Grundlegendes zur Identitätsregistrierung in Ihrer IoT Hub-Instanz](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aktualisieren des Modulzwillings mithilfe des Python-Dienst-SDK

In diesem Abschnitt erstellen Sie eine Python-Dienst-App, die die gewünschten Eigenschaften für den Modulzwilling aktualisiert.

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-hub** zu installieren. Sie können diesen Schritt überspringen, wenn Sie im vorherigen Abschnitt das Paket **azure-iot-hub** installiert haben.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsverzeichnis eine Datei mit dem Namen **UpdateModuleTwinDesiredProperties.py**.

1. Fügen Sie der Python-Datei den folgenden Code hinzu. Ersetzen Sie *YourIoTHubConnectionString* durch die Verbindungszeichenfolge, die Sie unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Abrufen von Updates auf Geräteseite

In diesem Abschnitt erstellen Sie eine Python-App, mit der die aktualisierten gewünschten Eigenschaften für den Modulzwilling auf Ihr Gerät abgerufen werden.

1. Rufen Sie die Modulverbindungszeichenfolge ab. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem IoT-Hub, und wählen Sie im linken Bereich **IoT-Geräte** aus. Wählen Sie in der Liste der Geräte **myFirstDevice** aus, und öffnen Sie es. Wählen Sie unter **Modulidentitäten** den Namen **myFirstModule** aus. Kopieren Sie die Modulverbindungszeichenfolge. Sie benötigen dieses Modul in einem folgenden Schritt.

   ![Moduldetails im Azure-Portal](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-device** zu installieren:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsverzeichnis eine Datei mit dem Namen **ReceiveModuleTwinDesiredPropertiesPatch.py**.

1. Fügen Sie der Python-Datei den folgenden Code hinzu. Ersetzen Sie *YourModuleConnectionString* durch die Modulverbindungszeichenfolge, die Sie in Schritt 1 kopiert haben.

    ```python
    import time
    from azure.iot.device import IoTHubModuleClient

    def twin_patch_handler(twin_patch):
        print("")
        print("Twin desired properties patch received:")
        print(twin_patch)


    def main():
        print ("Starting the IoT Hub Python sample...")
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

        print ("Waiting for commands, press Ctrl-C to exit")
        try:
            # Attach the handler to the client
            client.on_twin_desired_properties_patch_received = twin_patch_handler

            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()

    if __name__ == '__main__':
        main()
    ```

## <a name="run-the-apps"></a>Ausführen der Apps

In diesem Abschnitt führen Sie die Geräte-App **ReceiveModuleTwinDesiredPropertiesPatch** und dann die Dienst-App **UpdateModuleTwinDesiredProperties** aus, um die gewünschten Eigenschaften des Moduls zu aktualisieren.

1. Öffnen Sie eine Eingabeaufforderung, und führen Sie die Geräte-App aus:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Anfängliche Ausgabe der Geräte-App](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Öffnen Sie eine andere Eingabeaufforderung, und führen Sie die Dienst-App aus:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Beachten Sie, dass in der Ausgabe der Dienst-App die gewünschte **TelemetryInterval**-Eigenschaft im aktualisierten Modulzwilling angezeigt wird:

   ![Ausgabe der Dienst-App](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    In der Ausgabe der Geräte-App wird im empfangenen Patch für gewünschte Eigenschaften die gleiche Eigenschaft angezeigt:

   ![In der Ausgabe der Geräte-App wird der Patch für gewünschte Eigenschaften angezeigt.](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md)

* [Erste Schritte mit IoT Edge](../iot-edge/quickstart-linux.md)