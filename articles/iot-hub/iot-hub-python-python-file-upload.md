---
title: Hochladen von Dateien von Geräten nach Azure IoT Hub mit Python | Microsoft-Dokumentation
description: Informationen zum Hochladen von Dateien von einem Gerät in die Cloud mithilfe des Azure IoT-Geräte-SDK für Python. Hochgeladene Dateien werden in einem Azure Storage-Blobcontainer gespeichert.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/18/2021
ms.author: lizross
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d12ab40ee2bf585607f9dd60670e607decc4113a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548977"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (Python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In diesem Artikel wird gezeigt, wie eine Datei mithilfe der [Dateiuploadfunktionen von IoT Hub](iot-hub-devguide-file-upload.md) in [Azure Blob Storage](../storage/index.yml) hochgeladen wird. Das Tutorial veranschaulicht folgende Vorgehensweisen:

* Sicheres Angeben eines Speichercontainers zum Hochladen einer Datei

* Hochladen einer Datei über Ihren IoT Hub mit dem Python-Client

Der Schnellstart [Senden von Telemetriedaten von einem Gerät an einen IoT-Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) veranschaulicht die grundlegenden Gerät-zu-Cloud-Messagingfunktionen von IoT Hub. In einigen Szenarien können Sie allerdings nicht einfach die Daten, die Ihre Geräte senden, den relativ kleinen D2C-Nachrichten zuordnen, die IoT Hub akzeptiert. Wenn Sie Dateien von einem Gerät hochladen müssen, können Sie weiterhin die Sicherheit und Zuverlässigkeit des IoT Hub nutzen.

Am Ende dieses Tutorials führen Sie die folgende Python-Konsolen-App aus:

* **FileUpload.py**, die eine Datei mit dem Python-Geräte-SDK in den Speicher hochlädt.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Hochladen einer Datei von einer Geräte-App

In diesem Abschnitt erstellen Sie die Geräte-App zum Hochladen einer Datei in IoT Hub.

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket **azure-iot-device** zu installieren. Sie verwenden dieses Paket zur Koordination des Dateiuploads mit Ihrem IoT-Hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um das Paket [**azure.storage.blob**](https://pypi.org/project/azure-storage-blob/) zu installieren. Sie verwenden dieses Paket, um den Dateiupload durchzuführen.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Erstellen Sie eine Testdatei zum Hochladen in den Blobspeicher.

1. Erstellen Sie mit einem Text-Editor in Ihrem Arbeitsordner die Datei **FileUpload.py**.

1. Fügen Sie am Anfang der Datei **FileUpload.py** die folgenden `import`-Anweisungen und Variablen hinzu.

    ```python
    import os
    from azure.iot.device import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Ersetzen Sie in Ihrer Datei `[Device Connection String]` durch die Verbindungszeichenfolge Ihres IoT Hub-Geräts. Ersetzen Sie `[Full path to local file]` durch den Pfad zu der erstellten Testdatei oder einer Datei auf Ihrem Gerät, die Sie hochladen möchten.

1. Erstellen Sie eine Funktion, um die Datei in den Blobspeicher hochzuladen:

    ```python
    def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Diese Funktion analysiert die an sie übergebene Struktur *blob_info* und erstellt damit eine URL, über die sie einen [azure.storage.blob.BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) initialisiert. Anschließend lädt sie Ihre Datei mithilfe dieses Clients in Azure Blob Storage hoch.

1. Fügen Sie den folgenden Code hinzu, um eine Verbindung mit dem Client herzustellen und die Datei hochzuladen:

    ```python
    def run_sample(device_client):
        # Connect the client
        device_client.connect()

        # Get the storage info for the blob
        blob_name = os.path.basename(PATH_TO_FILE)
        storage_info = device_client.get_storage_info_for_blob(blob_name)

        # Upload to blob
        success, result = store_blob(storage_info, PATH_TO_FILE)

        if success == True:
            print("Upload succeeded. Result is: \n") 
            print(result)
            print()

            device_client.notify_blob_upload_status(
                storage_info["correlationId"], True, 200, "OK: {}".format(PATH_TO_FILE)
            )

        else :
            # If the upload was not successful, the result is the exception object
            print("Upload failed. Exception is: \n") 
            print(result)
            print()

            device_client.notify_blob_upload_status(
                storage_info["correlationId"], False, result.status_code, str(result)
            )

    def main():
        device_client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        try:
            print ("IoT Hub file upload sample, press Ctrl-C to exit")
            run_sample(device_client)
        except KeyboardInterrupt:
            print ("IoTHubDeviceClient sample stopped")
        finally:
            # Graceful exit
            device_client.shutdown()


    if __name__ == "__main__":
        main()
    ```

    Dieser Code erstellt einen **IoTHubDeviceClient** und verwendet die folgenden APIs, um den Dateiupload mit Ihrem IoT-Hub zu verwalten:

    * **get_storage_info_for_blob** ruft Informationen von Ihrem IoT-Hub über das zuvor erstellte verknüpfte Speicherkonto ab. Zu diesen Informationen gehören der Hostname, der Containername, der Blobname und ein SAS-Token. Die Speicherinformationen werden an die Funktion **store_blob** (die im vorherigen Schritt erstellt wurde) übermittelt, sodass sich der **BlobClient** in dieser Funktion bei Azure Storage authentifizieren kann. Die **get_storage_info_for_blob**-Methode gibt auch eine correlation_id zurück, die in der **notify_blob_upload_status**-Methode verwendet wird. Über die correlation_id gibt IoT Hub an, an welchem Blob Sie arbeiten.

    * **notify_blob_upload_status** benachrichtigt IoT Hub über den Status Ihres Blobspeichervorgangs. Sie übergeben die von der **get_storage_info_for_blob**-Methode abgerufene correlation_id. Mit ihr benachrichtigt IoT Hub Dienste, die möglicherweise nach einer Benachrichtigung über den Status der Dateiuploadaufgabe lauschen.

1. Speichern und schließen Sie die Datei **FileUpload.py**.

## <a name="run-the-application"></a>Ausführen der Anwendung

Jetzt können Sie die Anwendung ausführen.

1. Führen Sie an der Eingabeaufforderung in Ihrem Arbeitsordner den folgenden Befehl aus:

    ```cmd/sh
    python FileUpload.py
    ```

2. Der folgende Screenshot zeigt die Ausgabe der App **FileUpload**:

    ![Ausgabe der simulated-device-App](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Sie können das Verwaltungsportal verwenden, um die hochgeladene Datei im Speichercontainer anzuzeigen, den Sie konfiguriert haben:

    ![Hochgeladene Datei](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie die IoT Hub-Funktionen zum Hochladen von Dateien nutzen, um Dateiuploads zu vereinfachen. In den folgenden Artikeln werden weitere IoT Hub-Features und -Szenarien vorgestellt:

* [Erstellen einer IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage (PowerShell)](iot-hub-rm-template-powershell.md)

* [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDKs](iot-hub-devguide-sdks.md)

Unter folgenden Links finden Sie weitere Informationen zu Azure Blob Storage:

* [Dokumentation zu Azure Blob Storage](../storage/blobs/index.yml)

* [Dokumentation zur Azure Blob Storage-API für Python](/python/api/overview/azure/storage-blob-readme)