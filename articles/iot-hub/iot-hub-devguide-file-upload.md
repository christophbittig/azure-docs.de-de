---
title: Informationen zum Hochladen von Dateien in Azure IoT Hub | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Verwenden des Dateiupload-Features von IoT Hub zum Verwalten der Uploads von Dateien von einem Gerät in einen Azure Storage-Blobcontainer.'
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/25/2021
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 45913f6723cd9733e7c13fef769f1f445260ad00
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555109"
---
# <a name="upload-files-with-iot-hub"></a>Hochladen von Dateien mit IoT Hub

In einigen Szenarien können Sie allerdings nicht einfach die Daten, die Ihre Geräte senden, den relativ kleinen Gerät-zu-Cloud-Nachrichten zuordnen, die IoT Hub akzeptiert. Beispielsweise das Senden großer Mediendateien oder das Senden großer Telemetriesendungen, die entweder von zeitweilig verbundenen Geräten hochgeladen oder aggregiert und komprimiert wurden, um Bandbreite zu sparen.

Wenn Sie derartig große Dateien von einem Gerät hochladen müssen, können Sie weiterhin die Sicherheit und Zuverlässigkeit des IoT Hub nutzen. Statt Nachrichten über IoT Hub selbst zu übertragen, fungiert IoT Hub stattdessen als Verteiler für ein zugeordnetes Azure-Speicher-Konto. IoT Hub kann auch Benachrichtigungen an Back-End-Dienste bereitstellen, wenn ein Gerät einen Dateiupload abschließt.

Weitere Informationen zur Verwendung von gemeldeten Eigenschaften, D2C-Nachrichten oder Dateiuploads finden Sie im [Leitfaden für D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="file-upload-overview"></a>Übersicht über Dateiuploads

Ein IoT Hub erleichtert Dateiuploads von verbundenen Geräten, indem er SAS-URIs (Shared Access Signature) pro Upload für einen Blob-Container und ein Azure-Speicherkonto bereitstellt, die mit dem Hub vorkonfiguriert wurden. Die Verwendung von Dateiuploads mit IoT Hub besteht aus drei Teilen: Vorkonfigurieren eines Azure-Speicherkontos und Blob-Containers auf Ihrem IoT Hub, Hochladen von Dateien von Geräten und optional Benachrichtigen von Back-End-Diensten über abgeschlossene Dateiuploads.

Damit Sie die Dateiuploadfunktion in IoT Hub nutzen können, müssen Sie Ihrem IoT-Hub zuerst ein [Azure-Speicherkonto](/azure/storage/common/storage-account-overview) und einen [Blob-Container](/azure/storage/blobs/storage-blobs-introduction) zuordnen. Sie können auch Einstellungen konfigurieren, die steuern, wie sich der IoT Hub bei Azure Storage authentifiziert und die die Gültigkeitsdauer (Time-to-Live, TTL) der SAS-URIs, die der IoT Hub an Geräte übermittelt, und Benachrichtigungen über Dateiuploads an Ihre Back-End-Dienste steuern. Für mehr Informationen siehe [Zuordnen eines Azure-Speicher-Kontos zu einem IoT Hub](#associate-an-azure-storage-account-with-iot-hub).

Geräte führen einen dreistufigen Prozess aus, um eine Datei in den zugeordneten Blob-Container hochzuladen:

1. Das Gerät initiiert den Dateiupload mit dem IoT Hub. Er übergibt den Namen eines Blobs in der Anfrage und ruft im Gegenzug einen SAS-URI und eine Korrelations-ID ab. Der SAS-URI enthält ein SAS-Token für Azure Storage, das dem Gerät Lese-/Schreibberechtigung für das angeforderte Blob im Blob-Container erteilt. Weitere Informationen finden Sie unter [Gerät: Initialisieren eines Dateiuploads.](#device-initialize-a-file-upload)

1. Das Gerät verwendet den SAS-URI, um Azure-Blob-Speicher-APIs sicher aufzurufen, damit die Datei in den Blob-Container hochgeladen werden kann. Weitere Informationen finden Sie unter [Gerät: Hochladen der Datei mit Hilfe von Azure Storage APIs](#device-upload-file-using-azure-storage-apis).

1. Wenn der Dateiupload abgeschlossen ist, benachrichtigt das Gerät den IoT Hub über den Abschlussstatus unter Verwendung der Korrelations-ID, die es von IoT Hub beim Initiieren des Uploads erhalten hat. Für weitere Informationen siehe [Gerät: IoT Hub über einen abgeschlossenen Dateiupload benachrichtigen](#device-notify-iot-hub-of-a-completed-file-upload).

Back-End-Dienste können Dateiuploadbenachrichtigungen auf dem dienstseitigen Endpunkt für Dateiuploadbenachrichtigungen des IoT Hub abonnieren. Wenn Sie diese Benachrichtigungen auf Ihrem IoT Hub aktiviert haben, werden sie auf diesem Endpunkt übermittelt, wenn ein Gerät den Hub darüber informiert, dass ein Dateiupload abgeschlossen wurde. Dienste können diese Benachrichtigungen verwenden, um eine weitere Verarbeitung der Blobdaten einzuleiten. Weitere Informationen finden Sie unter [Dienst: Dateiuploadbenachrichtigungen.](#service-file-upload-notifications)

Das Hochladen von Dateien wird von den Azure-IoT-Geräte- und Dienst-SDKs vollständig unterstützt. Für weitere Informationen siehe [Dateiupload mit Hilfe eines SDKs](#file-upload-using-an-sdk).

### <a name="file-upload-quotas-and-limits"></a>Kontingente und Grenzwerte für Dateiuploads

IoT Hub erzwingt Drosselungsgrenzwerte für die Anzahl von Dateiuploads, die innerhalb eines bestimmten Zeitraums initiiert werden können. Der Schwellenwert basiert auf der SKU und der Anzahl der Einheiten Ihres IoT Hubs. Darüber hinaus ist jedes Gerät jeweils auf 10 gleichzeitige aktive Dateiuploads beschränkt. Weitere Informationen finden Sie unter [Drosselung und Kontingente](iot-hub-devguide-quotas-throttling.md).

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Zuordnen eines Azure-Speicher-Kontos zu einem IoT Hub

Damit Sie die Dateiuploadfunktion in IoT Hub nutzen können, müssen Sie Ihrem IoT-Hub zuerst ein Azure-Speicherkonto und einen Blob-Container zuordnen. Alle Dateiuploads von Geräten, die bei Ihrem IoT Hub registriert sind, werden in diesen Container übertragen. Informationen zum Konfigurieren eines Speicherkontos und Blob-Containers auf Ihrem IoT Hub finden Sie unter [Konfigurieren von Dateiuploads mit dem Azure-Portal](iot-hub-configure-file-upload.md), [Konfigurieren von Dateiuploads mit Azure CLI](iot-hub-configure-file-upload-cli.md)oder [Konfigurieren von Dateiuploads mit PowerShell.](iot-hub-configure-file-upload-powershell.md) Sie können auch die IoT Hub-Verwaltungs-APIs verwenden, um Dateiuploads programmgesteuert zu konfigurieren.

Wenn Sie das Portal verwenden, können Sie während der Konfiguration ein Speicherkonto und einen Container erstellen. Informationen zum Erstellen eines Speicherkontos finden Sie andernfalls unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) in der Azure Storage-Dokumentation. Sobald Sie über ein Speicherkonto verfügen, können Sie in den [Azure Blob-Storage-Schnellstarts](/azure/storage/blobs/storage-quickstart-blobs-portal) sehen, wie Sie einen Blob-Container erstellen.

Es gibt mehrere andere Einstellungen, die den Ablauf von Dateiuploads und Dateiuploadbenachrichtigungen steuern. In den folgenden Abschnitten sind alle verfügbaren Einstellungen aufgeführt. Je nachdem, ob Sie das Azure-Portal, Azure CLI, PowerShell oder die Verwaltungs-APIs zum Konfigurieren von Dateiuploads verwenden, sind einige dieser Einstellungen möglicherweise nicht verfügbar. Stellen Sie sicher, dass Sie die Einstellung **enableFileUploadNotifications** (Dateiuploadbenachrichtigungen aktivieren) festlegen, wenn Benachrichtigungen an Ihre Back-End-Dienste gesendet werden sollen und wenn ein Dateiupload abgeschlossen ist.

### <a name="iot-hub-storage-and-authentication-settings"></a>IoT Hub-Speicher und -Authentifizierungseinstellungen

Die folgenden Einstellungen ordnen Ihrem IoT Hub ein Speicherkonto und einen Container zu und steuern, wie sich Ihr Hub bei Azure Storage authentifiziert. Diese Einstellungen wirken sich nicht auf die Authentifizierung von Geräten mit Azure Storage aus. Geräte authentifizieren sich immer mit dem SAS-Token, das im SAS-URI angezeigt wird, der aus dem IoT Hub abgerufen wurde.

| Eigenschaft | BESCHREIBUNG | Bereich und Standardwert |
| --- | --- | --- |
| **storageEndpoints.$default.authenticationType** | Steuert, wie sich der IoT Hub bei Azure Storage authentifiziert. | Mögliche Werte sind keyBased und identityBased. Standardwert: keyBased. |
| **storageEndpoints.$default.connectionString** | Die Verbindungszeichenfolge für das Azure-Speicherkonto, das für Dateiuploads verwendet werden soll. | Standard: leere Zeichenfolge. |
| **storageEndpoints.$default.containerName** | Der Name des Containers, in den Daten hochgeladen werden sollen. | Standard: leere Zeichenfolge. |
| **storageEndpoints.$default.identity** | Die verwaltete Identität, die für die identitätsbasierte Authentifizierung verwendet werden soll. | Mögliche Werte sind `[system]` für die vom System zugewiesene verwaltete Identität oder eine Ressourcen-ID für eine vom Benutzer zugewiesene verwaltete Identität. Der Wert wird nicht für die schlüsselbasierte Authentifizierung verwendet. Standard: NULL |

### <a name="file-upload-settings"></a>Dateiuploadeinstellungen

Die folgenden Einstellungen steuern das Hochladen von Dateien vom Gerät.

| Eigenschaft | BESCHREIBUNG | Bereich und Standardwert |
| --- | --- | --- |
| **storageEndpoints.$default.ttlAsIso8601** | Standard-Gültigkeitsdauer für SAS-URIs, die von IoT Hub generiert werden. | ISO_8601-Intervall bis zu 48 Stunden (mindestens 1 Minute). Standardwert: 1 Stunde. |

### <a name="file-upload-notification-settings"></a>Einstellungen für Dateiuploadbenachrichtigungen

Mit den folgenden Einstellungen werden Dateiuploadbenachrichtigungen an Back-End-Dienste gesteuert.

| Eigenschaft | BESCHREIBUNG | Bereich und Standardwert |
| --- | --- | --- |
| **enableFileUploadNotifications** |Steuert, ob Dateiuploadbenachrichtigungen in den Endpunkt für Dateibenachrichtigungen geschrieben werden. |Bool. Standardwert: False. |
| **fileNotifications.ttlAsIso8601** |Standardgültigkeitsdauer für Dateiuploadbenachrichtigungen. |ISO_8601-Intervall bis zu 48 Stunden (mindestens 1 Minute). Standardwert: 1 Stunde. |
| **fileNotifications.lockDuration** |Sperrdauer für die Warteschlange der Dateiuploadbenachrichtigungen. |Möglicher Bereich: zwischen fünf und 300 Sekunden. Standardwert: 60 Sekunden. |
| **fileNotifications.maxDeliveryCount** |Maximale Übermittlungsanzahl für die Warteschlange der Dateiuploadbenachrichtigungen. |1 bis 100. Standardwert: 100. |

## <a name="file-upload-using-an-sdk"></a>Dateiupload mithilfe eines SDK

Die folgenden Schrittanleitungen enthalten eine vollständige Schritt-für-Schritt-Anleitung zum Hochladen von Dateien mithilfe der Azure IoT-Geräte- und Dienst-SDKs. Sie zeigen Ihnen, wie Sie das Azure-Portal verwenden, um ein Speicherkonto einem IoT Hub zuzuordnen, und sie enthalten Codeausschnitte oder verweisen auf Beispiele, die Sie durch einen Upload führen.

| Schrittanleitung | Geräte-SDK Beispiel | Dienst-SDK Beispiel |
|---------|--------|---------|
| [.NET](iot-hub-csharp-csharp-file-upload.md) | Ja | Ja |
| [Java](iot-hub-java-java-file-upload.md) | Ja | Ja |
| [Node.js](iot-hub-node-node-file-upload.md) | Ja | Ja |
| [Python](iot-hub-python-python-file-upload.md) | Ja | Nein (Nicht unterstützt) |

> [!NOTE]
> Das C-Geräte-SDK verwendet einen einzelnen Befehl auf dem Geräteclient, um Dateiuploads durchzuführen. Weitere Informationen finden Sie unter [IoTHubDeviceClient_UploadToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadtoblobasync) und [IoTHubDeviceClient_UploadMultipleBlocksToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadmultipleblockstoblobasync). Diese Funktionen führen alle Aspekte des Dateiuploads in einem einzigen Befehl aus – initiieren den Upload, laden die Daten in Azure Storage hoch und benachrichtigen IoT Hub, wenn der Vorgang abgeschlossen ist. Dies bedeutet, dass das Gerät zusätzlich zu dem Protokoll, das das Gerät für die Kommunikation mit dem IoT Hub verwendet, auch über HTTPS mit Azure Storage kommunizieren muss, da diese Funktionen Befehle an die Azure Storage-APIs geben.

## <a name="device-initialize-a-file-upload"></a>Gerät: Initialisieren eines Dateiuploads

Das Gerät ruft die REST-API zum [Erstellen von Dateiuploads SAS-URI\](/rest/api/iothub/device/create-file-upload-sas-uri) oder die entsprechende API in einem der Geräte-SDKs auf, um einen Dateiupload zu initiieren.

**Unterstützte Protokolle**: AMQP, AMQP-WS, MQTT, MQTT-WS und HTTPS <br/>
**Endpunkt**: {iot hub}.azure-devices.net/devices/{deviceId}/files <br/>
**Methode**: POST

```json
{
    "blobName":"myfile.txt"
}

```

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| Blob-Name | Der Name des Blobs, für das der SAS-URI generiert werden soll. |

 Der IoT Hub antwortet mit einer Korrelations-ID und den Elementen eines SAS-URI, die das Gerät für die Authentifizierung bei Azure Storage verwenden kann. Diese Antwort unterliegt den Drosselungsgrenzwerten und Uploadlimits pro Gerät des IoT Hub-Ziels.

```json
{
    "correlationId":"MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "hostName":"contosostorageaccount.blob.core.windows.net",
    "containerName":"device-upload-container",
    "blobName":"mydevice/myfile.txt",
    "sasToken":"?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw"
}

```

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| correlationId | Die Kennung für das Gerät, die beim Senden der Benachrichtigung über den abgeschlossenen Dateiupload an den IoT Hub verwendet werden soll. |
| hostName | Der Hostname des Azure-Speicherkontos für das Speicherkonto, das auf dem IoT Hub konfiguriert ist. |
| containerName | Der Name des Blob-Containers, der auf dem IoT Hub konfiguriert ist. |
| Blob-Name | Der Speicherort, an dem das Blob im Container gespeichert wird. Der Name weist folgendes Format auf: `{device ID of the device making the request}/{blobName in the request}` |
| sasToken> | Ein SAS-Token, das Lese-/Schreibzugriff auf das Blob bei Azure Storage gewährt. Das Token wird von IoT Hub generiert und signiert. |

Wenn die Antwort empfangen wird, macht das Gerät Folgendes:

* Es speichert die Korrelations-ID, die in die  Benachrichtigung über den abgeschlossenen Dateiupload an den IoT Hub eingefügt werden soll, wenn der Upload abgeschlossen ist.

* Es verwendet andere Eigenschaften, um einen SAS-URI für das Blob zu erstellen, das für die Authentifizierung bei Azure Storage verwendet wird. Der SAS-URI enthält den Ressourcen-URI für das angeforderte Blob und das SAS-Token. Sie hat folgende Form: `https://{hostName}/{containerName}/{blobName}{sasToken}` (Die `sasToken` Eigenschaft in der Antwort enthält ein vorangestelltes "?"-Zeichen.) Die Klammern sind nicht enthalten.

    Für die Werte, die im obigen Beispiel zurückgesandt werden, lautet der SAS-URI beispielsweise `https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw`

    Weitere Informationen zum SAS-URI und SAS-Token finden Sie unter [Erstellen einer Dienst-SAS](/rest/api/storageservices/create-service-sas) in der Azure Storage-Dokumentation.

## <a name="device-upload-file-using-azure-storage-apis"></a>Gerät: Datei mit Hilfe von Azure Speicher-APIs hochladen

Das Gerät verwendet die [Azure Blob-Storage-REST-APIs](/rest/api/storageservices/blob-service-rest-api) oder entsprechende Azure Speicher-SDK-APIs, um die Datei in das Blob in Azure Storage hochzuladen.

**Unterstützte Protokolle**: HTTPS

Das folgende Beispiel zeigt eine [Blob-Setzen-Anfrage](/rest/api/storageservices/put-blob) zum Erstellen oder Aktualisieren eines kleinen Blockblobs. Beachten Sie, dass der für diese Anforderung verwendete URI der SAS-URI ist, der von IoT Hub im vorherigen Abschnitt zurückgesandt wurde. Der `x-ms-blob-type` Header gibt an, dass diese Anfrage für ein Blockblob gilt. Wenn die Anfrage erfolgreich ist, gibt Azure Storage einen `201 Created` zurück.

```http
PUT https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw HTTP/1.1
Content-Length: 11
Content-Type: text/plain; charset=UTF-8
Host: contosostorageaccount.blob.core.windows.net
x-ms-blob-type: BlockBlob

hello world
```

Die Arbeit mit den Azure-Speicher-APIs würde den Rahmen dieses Abschnitts sprengen. Zusätzlich zu den REST-APIs für den Azure Blob Storage, die zuvor in diesem Abschnitt verknüpft wurden, können Sie die folgende Dokumentation lesen, um Ihnen den Einstieg zu erleichtern:

* Weitere Informationen zu den Blobs in Azure Storage finden Sie in der Dokumentation über den [Azure Blob-Speicher](/azure/storage/blobs/).

* Informationen zur Verwendung von Azure Storage-Client-SDKs zum Hochladen von Blobs finden Sie unter [Azure Blob-Speicher-API-Referenz](/azure/storage/blobs/reference).  

## <a name="device-notify-iot-hub-of-a-completed-file-upload"></a>Gerät: Benachrichtigen des IoT Hubs über einen abgeschlossenen Dateiupload

Das Gerät ruft die REST-API zum [Aktualisieren des Dateiuploadstatus ](/rest/api/iothub/device/update-file-upload-status) oder die entsprechende API in einem der Geräte-SDKs auf, wenn es den Dateiupload zum Abschluss bringt. Das Gerät sollte den Dateiuploadstatus mit IoT Hub aktualisieren, unabhängig davon, ob der Upload erfolgreich war oder fehlgeschlagen ist.

**Unterstützte Protokolle**: AMQP, AMQP-WS, MQTT, MQTT-WS und HTTPS <br/>
**Endpunkt**: {iot hub}.azure-devices.net/devices/{deviceId}/files <br/>
**Methode**: POST 

```json
{
    "correlationId": "MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "isSuccess": true,
    "statusCode": 200,
    "statusDescription": "File uploaded successfully"
}

```

 Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| correlationId | Die Korrelations-ID, die in der ersten SAS-URI-Anforderung empfangen wurde. |
| IsSuccess | Ein boolescher Wert, der angibt, ob der Dateiupload erfolgreich war. |
| statusCode | Eine ganze Zahl, die den Statuscode des Dateiuploads darstellt. In der Regel drei Ziffern; zum Beispiel 200 oder 201. |
| statusDescription | Eine Beschreibung des Dateiuploadstatus. |

Wenn eine Benachrichtigung über den abgeschlossenen Dateiupload vom Gerät empfangen wird, gibt es folgende Aktionen des IoT Hubs:

* Löst eine Dateiuploadbenachrichtigung an Back-End-Dienste aus, wenn Dateiuploadbenachrichtigungen konfiguriert sind.

* Gibt die dem Dateiupload zugeordneten Ressourcen frei. Ohne Benachrichtigungen verwaltet IoT Hub die Ressourcen, bis die dem Upload zugeordnete SAS-URI-Gültigkeitsdauer (Time-to-Live, TTL) abläuft.

## <a name="service-file-upload-notifications"></a>Service: Benachrichtigungen zum Dateiupload

Wenn Dateiuploadbenachrichtigungen auf Ihrem IoT Hub aktiviert sind, wird eine Benachrichtigung für Back-End-Dienste generiert, wenn von einem Gerät eine Benachrichtigung darüber empfangen wird, dass ein Dateiupload abgeschlossen ist. IoT Hub übermittelt diese Dateiuploadbenachrichtigungen über einen dienstseitigen Endpunkt. Die Empfangssemantik für Dateiuploadbenachrichtigungen entspricht der Empfangssemantik für C2D-Nachrichten und weist den gleichen [Nachrichtenlebenszyklus](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle) auf. Die Dienst-SDKs machen APIs verfügbar, um Dateiuploadbenachrichtigungen zu verarbeiten.

**Unterstützte Protokolle**: AMQP, AMQP-WS <br/>
**Endpunkt**: {iot hub}.azure-devices.net/messages/servicebound/fileuploadnotifications <br/>
**Methode**: GET

Jede Nachricht, die vom Endpunkt für Dateiuploadbenachrichtigungen abgerufen wird, ist ein JSON-Datensatz mit den folgenden Eigenschaften:

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt",
    "blobName":"mydevice/myfile.txt",
    "lastUpdatedTime":"2021-07-31T00:26:50+00:00",
    "blobSizeInBytes":11,
    "enqueuedTimeUtc":"2021-07-31T00:26:51.5134008Z"
}
```

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| EnqueuedTimeUtc | Zeitstempel, der die Erstellung der Benachrichtigung angibt. |
| deviceId | Die Geräte-ID des Geräts, das die Datei hochgeladen hat. |
| BlobUri | Der URI der hochgeladenen Datei. |
| Blob-Name | Der Name der hochgeladenen Datei. Der Name weist folgendes Format auf: `{device ID of the device}/{name of the blob}`|
| lastUpdatedTime |Zeitstempel, der die letzte Aktualisierung der Datei angibt. |
| BlobSizeInBytes | Eine ganze Zahl, die die Größe der hochgeladenen Datei in Bytes darstellt. |

Dienste können Benachrichtigungen zum Verwalten von Uploads verwenden. Beispielsweise können sie ihre eigene Verarbeitung der Blobdaten auslösen, die Verarbeitung der Blobdaten mit anderen Azure-Diensten auslösen oder die Dateiuploadbenachrichtigung zur späteren Überprüfung protokollieren.

## <a name="next-steps"></a>Nächste Schritte

* [Anleitungen zum Dateiupload](iot-hub-csharp-csharp-file-upload.md)

* [Konfigurieren von Dateiuploads auf IoT Hub](iot-hub-configure-file-upload.md)

* [Konfigurieren von verwalteten Identitäten auf IoT Hub](iot-hub-managed-identity.md)

* [Dokumentation zu Azure Blob Storage](/azure/storage/blobs/)

* Unter [Verstehen und Verwenden von Azure IoT Hub SDKs](iot-hub-devguide-sdks.md) werden die verschiedenen Sprach-SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit IoT Hub verwenden können.
