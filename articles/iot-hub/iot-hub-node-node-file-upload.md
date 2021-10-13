---
title: Hochladen von Dateien von Geräten nach Azure IoT Hub mit Node | Microsoft-Dokumentation
description: Informationen zum Hochladen von Dateien von einem Gerät in die Cloud mithilfe des Azure IoT-Geräte-SDK für Node.js Hochgeladene Dateien werden in einem Azure Storage-Blobcontainer gespeichert.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 07/27/2021
ms.custom: mqtt, devx-track-js
ms.openlocfilehash: a95737cd9d15b0ee21249f0db8d1bbb96fb8a3b2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710083"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-nodejs"></a>Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (Node.js)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Das Tutorial veranschaulicht folgende Vorgehensweisen:

* Sicheres Bereitstellen eines Geräts mit einem Azure-Blob-URI für das Hochladen einer Datei.

* Verwenden der IoT Hub-Dateihochlade-Benachrichtigungen zum Auslösen der Dateiverarbeitung in Ihrem App-Back-End.

Der Schnellstart [Senden von Telemetriedaten von einem Gerät an einen IoT-Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) veranschaulicht die grundlegenden Gerät-zu-Cloud-Messagingfunktionen von IoT Hub. In einigen Szenarien können Sie allerdings nicht einfach die Daten, die Ihre Geräte senden, den relativ kleinen Gerät-zu-Cloud-Nachrichten zuordnen, die IoT Hub akzeptiert. Beispiel:

* Große Dateien, die Bilder enthalten
* Videos
* Vibrationsdaten, die mit hoher Häufigkeit als Stichproben erfasst werden
* Eine Form vorverarbeiteter Daten.

Diese Dateien werden normalerweise als Batch in der Cloud mit Tools wie [Azure Data Factory](../data-factory/introduction.md) oder dem [Hadoop](../hdinsight/index.yml)-Stapel verarbeitet. Wenn Sie Dateien von einem Gerät hochladen müssen, können Sie weiterhin die Sicherheit und Zuverlässigkeit des IoT Hub nutzen.

Am Ende dieses Artikels führen Sie zwei Node.js-Konsolen-Apps aus:

* **FileUpload.js** lädt mithilfe eines SAS-URI, den Ihr IoT-Hub bereitstellt, eine Datei in den Speicher hoch.

* **FileUploadNotification.js** empfängt Benachrichtigungen zum Hochladen von Dateien von Ihrem IoT-Hub.

> [!NOTE]
> IoT Hub bietet über Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen, darunter C, Java, Python und JavaScript. Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Node.js, Version 10.0.x oder höher. Die LTS-Version wird empfohlen. Sie können Node.js von [nodejs.org](https://nodejs.org) herunterladen.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Hochladen einer Datei von einer Geräte-App

In diesem Abschnitt erstellen Sie eine Geräte-App zum Hochladen einer Datei in IoT-Hub. Der Code basiert auf Code, der in den [Azure IoT node.js SDK](https://github.com/Azure/azure-iot-sdk-node)-Gerätebeispielen im Beispiel [upload_to_blob_advanced.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/javascript/upload_to_blob_advanced.js) zur Verfügung steht.

1. Erstellen Sie einen leeren Ordner mit dem Namen `fileupload`.  Erstellen Sie im Ordner `fileupload` die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

    ```cmd/sh
    npm init
    ```

1. Führen Sie an der Eingabeaufforderung im Ordner `fileupload` den folgenden Befehl aus, um das Geräte-SDK **azure-iot-device**, das **azure-iot-device-mqtt** und die Pakete **@azure/storage-blob** zu installieren:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt @azure/storage-blob --save
    ```

1. Erstellen Sie im Ordner `fileupload` mithilfe eines Text-Editors eine Datei vom Typ **FileUpload.js**, und kopieren Sie den folgenden Code in den Ordner.

    ```javascript
    'use strict';

    const Client = require('azure-iot-device').Client;
    const Protocol = require('azure-iot-device-mqtt').Mqtt;
    const errors = require('azure-iot-common').errors;
    const path = require('path');

    const {
      AnonymousCredential,
      BlockBlobClient,
      newPipeline
    } = require('@azure/storage-blob');

    // make sure you set these environment variables prior to running the sample.
    const deviceConnectionString = process.env.DEVICE_CONNECTION_STRING;
    const localFilePath = process.env.PATH_TO_FILE;
    const storageBlobName = path.basename(localFilePath);

    async function uploadToBlob(localFilePath, client) {
      const blobInfo = await client.getBlobSharedAccessSignature(storageBlobName);
      if (!blobInfo) {
        throw new errors.ArgumentError('Invalid upload parameters');
      }

      const pipeline = newPipeline(new AnonymousCredential(), {
        retryOptions: { maxTries: 4 },
        telemetry: { value: 'HighLevelSample V1.0.0' }, // Customized telemetry string
        keepAliveOptions: { enable: false }
      });

      // Construct the blob URL to construct the blob client for file uploads
      const { hostName, containerName, blobName, sasToken } = blobInfo;
      const blobUrl = `https://${hostName}/${containerName}/${blobName}${sasToken}`;

      // Create the BlockBlobClient for file upload to the Blob Storage Blob
      const blobClient = new BlockBlobClient(blobUrl, pipeline);

      // Setup blank status notification arguments to be filled in on success/failure
      let isSuccess;
      let statusCode;
      let statusDescription;

      try {
        const uploadStatus = await blobClient.uploadFile(localFilePath);
        console.log('uploadStreamToBlockBlob success');

        // Save successful status notification arguments
        isSuccess = true;
        statusCode = uploadStatus._response.status;
        statusDescription = uploadStatus._response.bodyAsText;

        // Notify IoT Hub of upload to blob status (success)
        console.log('notifyBlobUploadStatus success');
      }
      catch (err) {
        isSuccess = false;
        statusCode = err.code;
        statusDescription = err.message;

        console.log('notifyBlobUploadStatus failed');
        console.log(err);
      }

      await client.notifyBlobUploadStatus(blobInfo.correlationId, isSuccess, statusCode, statusDescription);
    }

    // Create a client device from the connection string and upload the local file to blob storage.
    const deviceClient = Client.fromConnectionString(deviceConnectionString, Protocol);
    uploadToBlob(localFilePath, deviceClient)
      .catch((err) => {
        console.log(err);
      })
      .finally(() => {
        process.exit();
      });
    ```

1. Speichern und schließen Sie die Datei **FileUpload.js**.

1. Kopieren Sie eine Bilddatei in den Ordner `fileupload`, und geben Sie ihr einen Namen, etwa `myimage.png`.

1. Fügen Sie Umgebungsvariablen für Ihre Geräteverbindungszeichenfolge und den Pfad zur Datei hinzu, die Sie hochladen möchten. Sie haben die Geräteverbindungszeichenfolge erhalten, als Sie [das Gerät bei Ihrem IoT Hub registriert haben](#register-a-new-device-in-the-iot-hub).
    
    - Windows:

        ```cmd
        set DEVICE_CONNECTION_STRING={your device connection string}
        set PATH_TO_FILE={your image filepath}
        ```

    - Linux/Bash:

        ```bash
        export DEVICE_CONNECTION_STRING="{your device connection string}"
        export PATH_TO_FILE="{your image filepath}"
        ```

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst zum Empfangen von Dateiuploadbenachrichtigungen aus dem von Ihnen erstellten IoT-Hub. Damit Ihr Dienst Dateiuploadbenachrichtigungen empfangen kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Erhalten einer Benachrichtigung zum Dateiupload

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die Dateiuploadbenachrichtigungen von IoT Hub empfängt.

1. Erstellen Sie einen leeren Ordner mit dem Namen `fileuploadnotification`.  Erstellen Sie im Ordner `fileuploadnotification` die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

    ```cmd/sh
    npm init
    ```

1. Führen Sie an der Eingabeaufforderung im Ordner `fileuploadnotification` den folgenden Befehl aus, um das SDK-Paket **azure-iothub** zu installieren:

    ```cmd/sh
    npm install azure-iothub --save
    ```

1. Erstellen Sie mit einem Text-Editor eine **FileUploadNotification.js**-Datei im Ordner `fileuploadnotification`.

1. Fügen Sie am Anfang der Datei **FileUploadNotification.js** die folgenden `require`-Anweisungen hinzu:

    ```javascript
    'use strict';

    const Client = require('azure-iothub').Client;
    ```

1. Lesen Sie die Verbindungszeichenfolge für Ihren IoT-Hub aus der Umgebung:

    ```javascript
    const connectionString = process.env.IOT_HUB_CONNECTION_STRING;
    ```

1. Fügen Sie den folgenden Code hinzu, um einen Dienstclient aus dieser Zeichenfolge zu erstellen:

    ```javascript
    const serviceClient = Client.fromConnectionString(connectionString);
    ```

1. Öffnen Sie den Client, und verwenden Sie die **getFileNotificationReceiver**-Funktion zum Empfangen von Statusaktualisierungen.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFileNotificationReceiver(function receiveFileUploadNotification(err, receiver){
          if (err) {
            console.error('error getting the file notification receiver: ' + err.toString());
          } else {
            receiver.on('message', function (msg) {
              console.log('File upload from device:')
              console.log(msg.getData().toString('utf-8'));
            });
          }
        });
      }
    });
    ```

1. Speichern und schließen Sie die Datei **FileUploadNotification.js**.

1. Fügen Sie eine Umgebungsvariable für Ihre IoT Hub-Verbindungszeichenfolge hinzu. Sie haben diese Zeichenfolge zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert.
    
    - Windows:

        ```cmd
        set IOT_HUB_CONNECTION_STRING={your iot hub connection string}
        ```

    - Linux/Bash:

        ```bash
        export IOT_HUB_CONNECTION_STRING="{your iot hub connection string}"
        ```

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Jetzt können Sie die Anwendungen ausführen.

Führen Sie an der Eingabeaufforderung im `fileuploadnotification`-Ordner folgenden Befehl aus:

```cmd/sh
node FileUploadNotification.js
```

Führen Sie an der Eingabeaufforderung im `fileupload`-Ordner folgenden Befehl aus:

```cmd/sh
node FileUpload.js
```

Die folgende Ausgabe stammt aus der App **FileUpload**, nachdem der Upload abgeschlossen wurde:

```output
uploadStreamToBlockBlob success
notifyBlobUploadStatus success
```

Die folgende Beispielausgabe stammt aus der App **FileUploadNotification**, nachdem der Upload abgeschlossen wurde:

```output
Service client connected
File upload from device:
{"deviceId":"myDeviceId","blobUri":"https://{your storage account name}.blob.core.windows.net/device-upload-container/myDeviceId/image.png","blobName":"myDeviceId/image.png","lastUpdatedTime":"2021-07-23T23:27:06+00:00","blobSizeInBytes":26214,"enqueuedTimeUtc":"2021-07-23T23:27:07.2580791Z"}
```

## <a name="verify-the-file-upload"></a>Überprüfen des Dateiuploads

Sie können das Verwaltungsportal verwenden, um die hochgeladene Datei im Speichercontainer anzuzeigen, den Sie konfiguriert haben:

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
1. Wählen Sie im linken Bereich des Speicherkontos **Container** aus.
1. Wählen Sie den Container aus, in den Sie die Datei hochgeladen haben.
1. Wählen Sie den Ordner aus, der nach Ihrem Gerät benannt ist.
1. Wählen Sie das Blob aus, in das Sie ihre Datei hochgeladen haben. In diesem Artikel ist dies das Blob mit demselben Namen wie Ihre Datei.  

    :::image type="content" source="./media/iot-hub-node-node-file-upload/view-uploaded-file.png" alt-text="Screenshot: Anzeigen der hochgeladenen Datei im Azure-Portal":::

1. Zeigen Sie die Blobeigenschaften auf der Seite an, die dann geöffnet wird. Sie können **Herunterladen** auswählen, um die Datei herunterzuladen und ihren Inhalt lokal anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie die IoT Hub-Funktionen zum Hochladen von Dateien nutzen, um Dateiuploads zu vereinfachen. In den folgenden Artikeln werden weitere IoT Hub-Features und -Szenarien vorgestellt:

* [Erstellen einer IoT Hub-Instanz mithilfe einer Azure Resource Manager-Vorlage (PowerShell)](iot-hub-rm-template-powershell.md)

* [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDKs](iot-hub-devguide-sdks.md)
