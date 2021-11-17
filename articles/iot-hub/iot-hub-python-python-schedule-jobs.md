---
title: Planen von Aufträgen mit Azure IoT Hub (Python) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um eine direkte Methode auf mehreren Geräten aufzurufen. Sie verwenden die Azure IoT SDKs für Python, um die simulierten Geräte-Apps und eine Dienst-App für die Auftragsausführung zu implementieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: lizross
ms.custom: devx-track-python
ms.openlocfilehash: f87caf559815e14cbac4b6404eb255c65a89d83a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554995"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Planen und Übertragen von Aufträgen (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, mit dem eine Back-End-App Aufträge zur Planung und Aktualisierung von Millionen von Geräten erstellen und nachverfolgen kann.  Aufträge können für die folgenden Aktionen verwendet werden:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

Vom Konzept her umschließt ein Auftrag eine dieser Aktionen und verfolgt den Ausführungsfortschritt für eine Gruppe von Geräten nach, die anhand einer Gerätezwillingsabfrage definiert wird.  Mit einem Auftrag kann eine Back-End-App beispielsweise eine Neustartmethode auf 10.000 Geräten aufrufen – angegeben durch eine Gerätezwillingsabfrage und geplant für einen späteren Zeitpunkt.  Diese Anwendung kann dann den Fortschritt nachverfolgen, wenn diese Geräte jeweils die Neustartmethode empfangen und ausführen.

Weitere Informationen zu diesen Funktionen finden Sie in den folgenden Artikeln:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-python-twin-getstarted.md) und [Tutorial: Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)

* Direkte Methoden: [IoT Hub-Entwicklerhandbuch – direkte Methoden](iot-hub-devguide-direct-methods.md) und [Schnellstart: direkte Methoden](./quickstart-control-device.md?pivots=programming-language-python)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen einer simulierten Python-Geräte-App mit einer direkten Methode, um die Verwendung der **lockDoor**-Funktion zu ermöglichen, die vom Lösungs-Back-End aufgerufen werden kann

* Erstellen Sie eine Python-Konsolen-App, die die direkte **lockDoor**-Methode in der simulierten Geräte-App über einen Auftrag aufruft und die gewünschten Eigenschaften über einen Geräteauftrag aktualisiert.

Am Ende dieses Tutorials verfügen Sie über zwei Python-Apps:

**simDevice.py:** stellt mit der Geräteidentität eine Verbindung mit Ihrem IoT Hub her und empfängt eine direkte **lockDoor**-Methode.

**scheduleJobService.py:** ruft eine direkte Methode in der simulierten Geräte-App auf und aktualisiert die gewünschten Eigenschaften des Gerätezwillings per Auftrag.

> [!NOTE]
> Das **Azure IoT-SDK für Python** unterstützt die **Aufträge**-Funktion nicht direkt. Dieses Tutorial bietet stattdessen eine alternative Lösung mithilfe von asynchronen Threads und Timern. Weitere Updates finden Sie in der Featureliste des **Dienstclient-SDK** auf der Seite des [Azure IoT-SDK für Python](https://github.com/Azure/azure-iot-sdk-python).
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet. Dadurch wird eine simulierte **lockDoor**-Methode ausgelöst.

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-device** zu installieren:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsverzeichnis die Datei **simDevice.py**.

3. Fügen Sie am Anfang der Datei **simDevice.py** die folgenden `import`-Anweisungen und Variablen hinzu. Ersetzen Sie `deviceConnectionString` durch die Verbindungszeichenfolge des Geräts, das Sie oben erstellt haben:

    ```python
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Definieren Sie die folgende Funktion, die einen Client instanziiert und so konfiguriert, dass er auf die **lockDoor-Methode** reagiert und Gerätezwillingsupdates empfängt:

    ```python
    def create_client():
        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        # Define behavior for responding to the lockDoor direct method
        def method_request_handler(method_request):
            if method_request.name == "lockDoor":
                print("Locking Door!")

                resp_status = 200
                resp_payload = {"Response": "lockDoor called successfully"}
                method_response = MethodResponse.create_from_method_request(
                    method_request=method_request,
                    status=resp_status,
                    payload=resp_payload
                )
                client.send_method_response(method_response)

        # Define behavior for receiving a twin patch
        def twin_patch_handler(twin_patch):
            print("")
            print("Twin desired properties patch received:")
            print(twin_patch)

        # Set the handlers on the client
        try:
            print("Beginning to listen for 'lockDoor' direct method invocations...")
            client.on_method_request_received = method_request_handler
            print("Beginning to listen for updates to the Twin desired properties...")
            client.on_twin_desired_properties_patch_received = twin_patch_handler
        except:
            # If something goes wrong while setting the handlers, clean up the client
            client.shutdown()
            raise
    ```

5. Fügen Sie den folgenden Code hinzu, um das Beispiel auszuführen:

    ```python
    def main():
        print ("Starting the IoT Hub Python jobs sample...")
        client = create_client()

        print ("IoTHubDeviceClient waiting for commands, press Ctrl-C to exit")
        try:
            while True:
                time.sleep(100)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped!")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()


    if __name__ == '__main__':
        main()
    ```

6. Speichern und schließen Sie die Datei **simDevice.py**.

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
>

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, der eine direkte Methode auf einem Gerät aufruft und den Gerätezwilling aktualisiert. Für den Dienst wird die Berechtigung **Dienstverbindung** benötigt, um eine direkte Methode auf einem Gerät aufzurufen. Darüber hinaus sind für den Dienst auch die Berechtigungen **Lesevorgänge in Registrierung** und **Schreibvorgänge in Registrierung** erforderlich, um Lese- und Schreibvorgänge für die Identitätsregistrierung durchführen zu können. Da keine SAS-Standardrichtlinie ausschließlich mit diesen Berechtigungen zur Verfügung steht, müssen Sie eine erstellen.

Erstellen Sie wie folgt eine SAS-Richtlinie, die die Berechtigungen **Dienstverbindung**, **Lesevorgänge in Registrierung** und **Schreibvorgänge in Registrierung** gewährt, und rufen Sie eine Verbindungszeichenfolge für diese Richtlinie ab:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihren IoT-Hub. Wählen Sie dazu am besten **Ressourcengruppen**, anschließend die Ressourcengruppe, in der sich Ihr IoT-Hub befindet, und dann in der Ressourcenliste diesen IoT-Hub aus.

2. Wählen Sie im linken Bereich Ihres IoT-Hubs **Freigegebene Zugriffsrichtlinien** aus.

3. Wählen Sie im Menü über der Richtlinienliste die Option **Hinzufügen** aus.

4. Geben Sie im Bereich **Richtlinie für den gemeinsamen Zugriff hinzufügen** einen aussagekräftigen Namen für Ihre Richtlinie ein (z. B. *serviceAndRegistryReadWrite*). Wählen Sie unter **Berechtigungen** die Optionen **Dienstverbindung** und **Schreibvorgänge in Registrierung** aus (**Lesevorgänge in Registrierung** wird bei Auswahl von **Schreibvorgänge in Registrierung** automatisch ausgewählt). Klicken Sie anschließend auf **Erstellen**.

    ![Hinzufügen einer neuen SAS-Richtlinie](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Wählen Sie im Bereich **SAS-Richtlinien** in der Richtlinienliste Ihre neue Richtlinie aus.

6. Wählen Sie unter **Schlüssel für gemeinsamen Zugriff** das Kopiersymbol für **Verbindungszeichenfolge – Primärschlüssel** aus, und speichern Sie den Wert.

    ![Abrufen der Verbindungszeichenfolge.](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Weitere Informationen zu SAS-Richtlinien und Berechtigungen für IoT-Hubs finden Sie unter [Access Control und Berechtigungen](./iot-hub-dev-guide-sas.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planen von Aufträgen zum Aufrufen einer direkten Methode und Aktualisieren der Eigenschaften eines Gerätezwillings

In diesem Abschnitt erstellen Sie eine Python-Konsolen-App, mit der eine **lockDoor**-Remotefunktion auf einem Gerät mit einer direkten Methode initiiert wird, und aktualisieren außerdem die gewünschten Eigenschaften des Gerätezwillings.

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-hub** zu installieren:

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsverzeichnis die Datei **scheduleJobService.py**.

3. Fügen Sie am Anfang der Datei **scheduleJobService.py** die folgenden `import`-Anweisungen und Variablen hinzu. Ersetzen Sie den Platzhalter `{IoTHubConnectionString}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben. Ersetzen Sie den Platzhalter `{deviceId}` durch die Geräte-ID, die Sie unter [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) registriert haben:

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Fügen Sie die folgende Funktion hinzu, mit der Geräte abgefragt werden:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Fügen Sie die folgenden Methoden hinzu, um die Aufträge zum Aufrufen der direkten Methode und des Gerätezwillings auszuführen:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Fügen Sie den folgenden Code hinzu, um die Aufträge zu planen und den Auftragsstatus zu aktualisieren. Schließen Sie auch die Routine `main` ein:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Speichern und schließen Sie die Datei **scheduleJobService.py**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an der Eingabeaufforderung in Ihrem Arbeitsverzeichnis den folgenden Befehl aus, um mit dem Lauschen auf die direkte Methode zum Neustarten zu beginnen:

    ```cmd/sh
    python simDevice.py
    ```

2. Führen Sie an einer weiteren Eingabeaufforderung in Ihrem Arbeitsverzeichnis den folgenden Befehl aus, um die Aufträge zum Absperren der Tür und zum Aktualisieren des Zwillings auszulösen:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Die Antworten des Geräts auf die Aktualisierungen der direkten Methode und der Gerätezwillinge wird in der Konsole angezeigt.

    ![IoT Hub-Auftrag Beispiel 1 – Geräteausgabe](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub-Auftrag Beispiel 2 – Geräteausgabe](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Auftrag zum Planen einer direkten Methode für ein Gerät und eines Updates der Eigenschaften eines Gerätezwillings verwendet.

Informationen zu den weiteren ersten Schritten mit IoT Hub und Geräteverwaltungsmustern wie z. B. einem imagebasierten End-to-End-Update finden Sie im [Tutorial „Device Update for Azure IoT Hub unter Verwendung des Raspberry Pi 3 B+-Referenzimages“](../iot-hub-device-update/device-update-raspberry-pi.md).