---
title: Hochladen von Dateien von Geräten nach Azure IoT Hub mit .NET | Microsoft-Dokumentation
description: Informationen zum Hochladen von Dateien von einem Gerät in die Cloud mithilfe des Azure IoT-Geräte-SDK für .NET. Hochgeladene Dateien werden in einem Azure Storage-Blobcontainer gespeichert.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: c797c2668a1af750f0cb1ae4a743f1bc4b9f3aac
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556095"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>Hochladen von Dateien von Ihrem Gerät in die Cloud mit IoT Hub (.NET)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

In diesem Tutorial lernen Sie, wie Sie das IoT Hub-Feature zum Hochladen von Dateien bei den Azure IoT .NET-Geräte- und -Dienst-SDKs nutzen können.

Die Schnellstartanleitung [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (C#)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) und das Tutorial [Senden von Nachrichten aus der Cloud an das Gerät mit IoT Hub (.NET)](iot-hub-csharp-csharp-c2d.md) veranschaulichen die grundlegenden Gerät-zu-Cloud- und Cloud-zu-Gerät-Messagingfunktionen von IoT Hub. Im Tutorial [Konfigurieren der Nachrichtenweiterleitung mit IoT Hub](tutorial-routing.md) wird eine Möglichkeit für das zuverlässige Speichern von Gerät-zu-Cloud-Nachrichten in Microsoft Azure Blob Storage beschrieben. In einigen Szenarien können Sie allerdings nicht einfach die Daten, die Ihre Geräte senden, den relativ kleinen Gerät-zu-Cloud-Nachrichten zuordnen, die IoT Hub akzeptiert. Beispiel:

* Große Dateien, die Bilder enthalten

* Videos

* Vibrationsdaten, die mit hoher Häufigkeit als Stichproben erfasst werden

* Eine Form vorverarbeiteter Daten

Diese Dateien werden normalerweise als Batch in der Cloud mit Tools wie [Azure Data Factory](../data-factory/introduction.md) oder dem [Hadoop](../hdinsight/index.yml)-Stapel verarbeitet. Wenn Sie Dateien von einem Gerät hochladen müssen, können Sie aber weiterhin die Sicherheit und Zuverlässigkeit des IoT Hub nutzen. In diesem Tutorial erfahren Sie, wie.

Am Ende dieses Tutorials führen Sie zwei .NET-Konsolen-Apps aus:

* **FileUploadSample**. Diese Geräte-App lädt mithilfe eines SAS-URI, den Ihr IoT Hub bereitstellt, eine Datei in den Speicher hoch. Sie führen diese App aus dem Azure IoT C#-Beispiel-Repository aus, das Sie unter „Voraussetzungen“ herunterladen.

* **ReadFileUploadNotification**. Dieser Dienst empfängt von Ihrem IoT-Hub Benachrichtigungen zum Dateiupload. Sie werden diese App erstellen.

> [!NOTE]
> IoT Hub bietet über Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen, darunter C, Java, Python und JavaScript. Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit Azure IoT Hub.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Die in diesem Artikel ausgeführten Beispielanwendungen sind in C# geschrieben. Für die Azure IoT C#-Beispiele empfehlen wir, dass Sie auf Ihrem Entwicklungscomputer das .NET Core SDK 3.1 oder höher verwenden.

    Sie können das .NET Core SDK für mehrere Plattformen von [.NET](https://dotnet.microsoft.com/download) herunterladen.

    Mit dem folgenden Befehl können Sie die aktuelle Version des .NET Core SDKs auf Ihrem Entwicklungscomputer überprüfen:

    ```cmd/sh
    dotnet --version
    ```

* Laden Sie unter [https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip) die Azure IoT-C#-Beispiele herunter, und extrahieren Sie das ZIP-Archiv.

* Stellen Sie sicher, dass Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispiel in diesem Artikel wird das MQTT-Protokoll verwendet, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="upload-file-from-a-device-app"></a>Hochladen einer Datei aus einer Geräte-App

In diesem Artikel verwenden Sie ein Beispiel aus dem Repository mit Azure IoT C#-Beispielen, das Sie zuvor als Geräte-App heruntergeladen haben. Sie können die unten aufgeführten Dateien mithilfe von Visual Studio, Visual Studio Code oder eines Text-Editors Ihrer Wahl öffnen.  

Das Beispiel ist in **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample** unter dem Ordner zu finden, in den Sie die Azure IoT C#-Beispiele extrahiert haben.

Untersuchen Sie den Code in **FileUpLoadSample.cs**. Diese Datei enthält die Haupt-Beispiellogik. Nach dem Erstellen eines IoT Hub-Geräteclients wird das dreiteilige Standardverfahren zum Hochladen von Dateien von einem Gerät ausgeführt:

1. Der Code ruft auf dem Geräte-Client die Methode **GetFileUploadSasUriAsync** auf, um einen SAS URI aus dem IoT-Hub zu erhalten:

    ```csharp
    var fileUploadSasUriRequest = new FileUploadSasUriRequest
    {
        BlobName = fileName
    };

    // Lines removed for clarity

    FileUploadSasUriResponse sasUri = await _deviceClient.GetFileUploadSasUriAsync(fileUploadSasUriRequest);
    Uri uploadUri = sasUri.GetBlobUri();
    ```

1. Der Code verwendet den SAS-URI zum Hochladen der Datei in Azure Storage. In diesem Beispiel verwendet er den SAS-URI zum Erstellen eines Azure Storage-Blockblobclients und lädt die Datei hoch:

    ```csharp
    var blockBlobClient = new BlockBlobClient(uploadUri);
    await blockBlobClient.UploadAsync(fileStreamSource, new BlobUploadOptions());
    ```

1. Der Code benachrichtigt den IoT-Hub, dass der Upload abgeschlossen wurde. Dadurch wird der IoT-Hub informiert, dass er Ressourcen freigeben kann, die dem Upload zugeordnet sind (der SAS-URI). Wenn Dateiuploadbenachrichtigungen aktiviert sind, sendet der IoT-Hub eine Benachrichtigungsmeldung an Back-End-Dienste.

    ```csharp
    var successfulFileUploadCompletionNotification = new FileUploadCompletionNotification
    {
        // Mandatory. Must be the same value as the correlation id returned in the sas uri response
        CorrelationId = sasUri.CorrelationId,
    
        // Mandatory. Will be present when service client receives this file upload notification
        IsSuccess = true,
    
        // Optional, user defined status code. Will be present when service client receives this file upload notification
        StatusCode = 200,
    
        // Optional, user-defined status description. Will be present when service client receives this file upload notification
        StatusDescription = "Success"
    };
    
    await _deviceClient.CompleteFileUploadAsync(successfulFileUploadCompletionNotification);
    ```

Wenn Sie die Datei **parameter.cs** untersuchen, sehen Sie Folgendes:

- Bei diesem Beispiel müssen Sie den Parameter *p* übergeben, für den eine Geräteverbindungszeichenfolge erforderlich ist. 

- Standardmäßig verwendet das Gerätebeispiel das MQTT-Protokoll zum Kommunizieren mit IoT Hub. Sie können dieses Transportprotokoll mithilfe des Parameters *t* ändern. Beachten Sie, dass der Azure-Blobclient – unabhängig von dieser Auswahl – immer HTTPS als Protokoll zum Hochladen der Azure Storage-Datei verwendet.

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst zum Empfangen von Dateiuploadbenachrichtigungen aus Ihrem IoT-Hub. Damit Ihr Dienst Dateiuploadbenachrichtigungen empfangen kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Erhalten einer Benachrichtigung zum Dateiupload

In diesem Abschnitt erstellen Sie eine C#-Konsolen-App, die Dateiuploadbenachrichtigungen aus Ihrem IoT-Hub empfängt.

1. Öffnen Sie ein Befehlsfenster, und wechseln Sie zu dem Ordner, in dem Sie das Projekt erstellen möchten. Erstellen Sie den Ordner **ReadFileUploadNotifications**, und ändern Sie die Verzeichnisse auf diesen Ordner.

    ```cmd/sh
    mkdir ReadFileUploadNotification
    cd ReadFileUploadNotification
    ```

1. Führen Sie den folgenden Befehl zum Erstellen eines C#-Konsolenprojekts aus. Nach Ausführung des Befehls enthält der Ordner die Dateien **Program.cs** und **ReadFileUploadNotification.csproj**.

    ```cmd/sh
    dotnet new console --language c#
    ```

1. Führen Sie den folgenden Befehl aus, um das Paket **Microsoft.Azure.Devices** zur Projektdatei hinzuzufügen. Dieses Paket ist das Azure IoT .NET-Dienst-SDK.

    ```cmd/sh
    dotnet add package Microsoft.Azure.Devices
    ```

1. Öffnen Sie die Datei **Program.cs**, und fügen Sie Ihr am Anfang die folgende Anweisung hinzu:

    ```csharp
    using Microsoft.Azure.Devices;
    ```
1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert `{iot hub connection string}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();
        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();
            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Beachten Sie, dass das Empfangsmuster mit dem Muster zum Empfangen von Cloud-zu-Gerät-Nachrichten von der Geräte-App identisch ist.

1. Ersetzen Sie schließlich die Zeilen in der **Main**-Methode durch die folgenden:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```
    
## <a name="run-the-applications"></a>Ausführen der Anwendungen

Jetzt können Sie die Anwendungen ausführen.

1. Führen Sie zuerst die Dienst-App aus, um Dateiuploadbenachrichtigungen aus dem IoT-Hub zu empfangen. Führen Sie im Ordner **ReadFileUploadNotification** an Ihrer Eingabeaufforderung die folgenden Befehle aus: 

    ```cmd/sh
    dotnet restore
    dotnet run
    ```
    
    Die App startet und wartet auf eine Dateiuploadbenachrichtigung aus Ihrem IoT-Hub:

    ```cmd/sh
    Receive file upload notifications


    Receiving file upload notification from service
    Press Enter to exit
    ```



1. Führen Sie als Nächstes die Geräte-App aus, um die Datei in Azure Storage hochzuladen. Öffnen Sie eine neue Eingabeaufforderung, und ändern Sie die Ordner unter dem Ordner, in den Sie die Azure IoT C#-Beispiele extrahiert haben, in **azure-iot-samples-csharp-main\iot-hub\Samples\device\FileUploadSample**. Führen Sie die folgenden Befehle aus: Ersetzen Sie den Platzhalterwert `{Your device connection string}` im zweiten Befehl durch die Geräteverbindungszeichenfolge, die Sie vorher im Abschnitt [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) kopiert haben.

    ```cmd/sh
    dotnet restore
    dotnet run --p "{Your device connection string}"
    ```
    
    Die folgende Ausgabe stammt aus der Geräte-App, nachdem der Upload abgeschlossen wurde:
    
    ```cmd/sh
      Uploading file TestPayload.txt
      Getting SAS URI from IoT Hub to use when uploading the file...
      Successfully got SAS URI (https://contosostorage.blob.core.windows.net/contosocontainer/MyDevice%2FTestPayload.txt?sv=2018-03-28&sr=b&sig=x0G1Baf%2BAjR%2BTg3nW34zDNKs07p6dLzkxvZ3ZSmjIhw%3D&se=2021-05-04T16%3A40%3A52Z&sp=rw) from IoT Hub
      Uploading file TestPayload.txt using the Azure Storage SDK and the retrieved SAS URI for authentication
      Successfully uploaded the file to Azure Storage
      Notified IoT Hub that the file upload succeeded and that the SAS URI can be freed.
      Time to upload file: 00:00:01.5077954.
      Done.
    ```
    
1. Beachten Sie: Die Dienst-App zeigt, dass sie die Dateiuploadbenachrichtigung empfangen hat:

    ```cmd/sh
    Receive file upload notifications
    
    
    Receiving file upload notification from service
    Press Enter to exit
    
    Received file upload notification: myDeviceId/TestPayload.txt
    ```

## <a name="verify-the-file-upload"></a>Überprüfen des Dateiuploads

Sie können das Verwaltungsportal verwenden, um die hochgeladene Datei im Speichercontainer anzuzeigen, den Sie konfiguriert haben:

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
1. Wählen Sie im linken Bereich des Speicherkontos **Container** aus.
1. Wählen Sie den Container aus, in den Sie die Datei hochgeladen haben.
1. Wählen Sie den Ordner aus, der nach Ihrem Gerät benannt ist.
1. Wählen Sie das Blob aus, in das Sie ihre Datei hochgeladen haben. In diesem Artikel ist es das Blob **TestPayload.txt**.  

    :::image type="content" source="./media/iot-hub-csharp-csharp-file-upload/view-uploaded-file.png" alt-text="Screenshot: Auswählen der hochgeladenen Datei im Azure-Portal.":::

1. Zeigen Sie die Blobeigenschaften auf der Seite an, die dann geöffnet wird. Sie können **Herunterladen** auswählen, um die Datei herunterzuladen und ihren Inhalt lokal anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie das IoT Hub-Feature zum Hochladen von Dateien nutzen, um Dateiuploads von Geräten zu vereinfachen. In den folgenden Artikeln können Sie mehr zu diesem Feature erfahren:

* [Übersicht über Dateiuploads mit IoT Hub](iot-hub-devguide-file-upload.md)

* [Konfigurieren von IoT Hub-Dateiuploads](iot-hub-configure-file-upload.md)

* [Dokumentation zu Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)

* [Azure Blob Storage: API-Referenz](../storage/blobs/reference.md)

* [Azure IoT SDKs](iot-hub-devguide-sdks.md)