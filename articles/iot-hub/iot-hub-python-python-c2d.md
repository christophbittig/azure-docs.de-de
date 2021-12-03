---
title: C2D-Nachrichten mit Azure IoT Hub (Python) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Python C2D-Nachrichten von einem Azure IoT Hub an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: lizross
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: de60d0c92ee44cf2e58d11b2e28d9c2dfccdc0b1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551594"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Senden von C2D-Nachrichten mit IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. In [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) erfahren Sie, wie Sie einen IoT-Hub erstellen, eine Geräteidentität darin bereitstellen und eine simulierte Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Python-Konsolen-Apps aus:

* **SimulatedDevice.py** ist eine modifizierte Version der in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) erstellten App. Sie stellt eine Verbindung mit Ihrer IoT Hub-Instanz her und empfängt C2D-Nachrichten.

* **SendCloudToDeviceMessage.py**, die über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App sendet.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App zum Simulieren des Geräts und empfangen C2D-Nachrichten aus dem IoT Hub.

1. Installieren Sie über eine Eingabeaufforderung in Ihrem Arbeitsverzeichnis das **Azure IoT Hub Device SDK für Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Erstellen Sie in einem Text-Editor eine Datei namens **SimulatedDevice.py**.

1. Fügen Sie am Anfang der Datei **SimulatedDevice.py** die folgenden `import`-Anweisungen und -Variablen hinzu:

    ```python
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Fügen Sie der Datei **SimulatedDevice.py** dann folgenden Code hinzu. Ersetzen Sie den Platzhalterwert `{deviceConnectionString}` durch die Verbindungszeichenfolge für das Gerät, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) erstellt haben:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Definieren Sie die folgende Funktion, mit der empfangene Nachrichten an der Konsole ausgegeben werden:

    ```python
    def message_handler(message):
        global RECEIVED_MESSAGES
        RECEIVED_MESSAGES += 1
        print("")
        print("Message received:")

        # print data from both system and application (custom) properties
        for property in vars(message).items():
            print ("    {}".format(property))

        print("Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

1. Fügen Sie den folgenden Code hinzu, um den Client zu initialisieren und auf den Empfang der C2D-Nachricht zu warten:

    ```python
    def main():
        print ("Starting the Python IoT Hub C2D Messaging device sample...")

        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        print ("Waiting for C2D messages, press Ctrl-C to exit")
        try:
            # Attach the handler to the client
            client.on_message_received = message_handler

            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoT Hub C2D Messaging device sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()
    ```

1. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        main()
    ```

1. Speichern und schließen Sie die Datei **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, um C2D-Nachrichten über die IoT Hub-Instanz zu senden, die Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) erstellt haben. Damit Ihr Dienst Cloud-zu-Gerät-Nachrichten senden kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) erstellt haben. Außerdem benötigen Sie die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis eine Eingabeaufforderung, und installieren Sie das **Azure IoT Hub Service SDK für Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Erstellen Sie in einem Text-Editor eine Datei namens **SendCloudToDeviceMessage.py**.

1. Fügen Sie am Anfang der Datei **SendCloudToDeviceMessage.py** die folgenden `import`-Anweisungen und -Variablen hinzu:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Fügen Sie der Datei **SendCloudToDeviceMessage.py** dann folgenden Code hinzu. Ersetzen Sie die Platzhalterwerte `{iot hub connection string}` und `{device id}` durch die IoT-Hub-Verbindungszeichenfolge bzw. durch die Geräte-ID, die Sie zuvor notiert haben:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Fügen Sie den folgenden Code hinzu, um Nachrichten an Ihr Gerät zu senden:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Fügen Sie die folgende main-Funktion hinzu:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Speichern und schließen Sie die Datei **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an der Eingabeaufforderung in Ihrem Arbeitsverzeichnis den folgenden Befehl aus, um auf C2D-Nachrichten zu lauschen:

    ```shell
    python SimulatedDevice.py
    ```

    ![Ausführen der simulierten Geräte-App](./media/iot-hub-python-python-c2d/device-1.png)

1. Öffnen Sie eine neue Eingabeaufforderung in Ihrem Arbeitsverzeichnis, und führen Sie den folgenden Befehl aus, um C2D-Nachrichten zu senden:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Ausführen der App zum Senden des C2D-Befehls](./media/iot-hub-python-python-c2d/service.png)

1. Beachten Sie die vom Gerät empfangene Nachricht.

    ![Empfangene Nachricht](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden.

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).