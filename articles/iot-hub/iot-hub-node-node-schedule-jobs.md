---
title: Planen von Aufträgen mit Azure IoT Hub (Node) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um eine direkte Methode auf mehreren Geräten aufzurufen. Sie verwenden die Azure IoT SDKs für Node.js, um die simulierten Geräte-Apps und eine Dienst-App für die Auftragsausführung zu implementieren.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/16/2019
ms.custom: mqtt, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 6061b0ccba7448bbb51dcc93bacecce27c63b910
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179277"
---
# <a name="schedule-and-broadcast-jobs-nodejs"></a>Planen und Übertragen von Aufträgen (Node.js)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, mit dem eine Back-End-App Aufträge zur Planung und Aktualisierung von Millionen von Geräten erstellen und nachverfolgen kann.  Aufträge können für die folgenden Aktionen verwendet werden:

* Aktualisieren gewünschter Eigenschaften
* Aktualisieren von Tags
* Aufrufen direkter Methoden

Vom Konzept her umschließt ein Auftrag eine dieser Aktionen und verfolgt den Ausführungsfortschritt für eine Gruppe von Geräten nach, die anhand einer Gerätezwillingsabfrage definiert wird.  Mit einem Auftrag kann eine Back-End-App beispielsweise eine Neustartmethode auf 10.000 Geräten aufrufen – angegeben durch eine Gerätezwillingsabfrage und geplant für einen späteren Zeitpunkt. Diese Anwendung kann dann den Fortschritt nachverfolgen, wenn diese Geräte jeweils die Neustartmethode empfangen und ausführen.

Weitere Informationen zu diesen Funktionen finden Sie in den folgenden Artikeln:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-node-node-twin-getstarted.md) und [Tutorial: Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)

* Direkte Methoden: [IoT Hub-Entwicklerhandbuch – direkte Methoden](iot-hub-devguide-direct-methods.md) und [Schnellstart: direkte Methoden](./quickstart-control-device.md?pivots=programming-language-nodejs)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen einer simulierten Node.js-Geräte-App mit einer direkten Methode, um die Verwendung der **lockDoor**-Funktion zu ermöglichen, die vom Lösungs-Back-End aufgerufen werden kann

* Erstellen einer Node.js-Konsolen-App, die die direkte **lockDoor**-Methode in der simulierten Geräte-App über einen Auftrag aufruft und die gewünschten Eigenschaften über einen Geräteauftrag aktualisiert

Am Ende dieses Tutorials verfügen Sie über zwei Node.js-Apps:

* **simDevice.js**: Stellt mit der Geräteidentität eine Verbindung mit Ihrem IoT Hub her und empfängt eine direkte **lockDoor**-Methode.

* **scheduleJobService.js**: Ruft eine direkte Methode in der simulierten Geräte-App auf und aktualisiert die gewünschten Eigenschaften des Gerätezwillings per Auftrag.

## <a name="prerequisites"></a>Voraussetzungen

* Node.js, Version 10.0.x oder höher. Unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.)

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet. Dadurch wird eine simulierte **lockDoor**-Methode ausgelöst.

1. Erstellen Sie einen leeren Ordner mit dem Namen **simDevice**.  Erstellen Sie im Ordner **simDevice** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

   ```console
   npm init
   ```

2. Führen Sie an der Eingabeaufforderung im Ordner **simDevice** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iot-device** und das Paket **azure-iot-device-mqtt** zu installieren:

   ```console
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Erstellen Sie mit einem Text-Editor im Ordner **simDevice** die neue Datei **simDevice.js**.

4. Fügen Sie am Anfang der Datei **simDevice.js** die folgenden require-Anweisungen hinzu:

    ```javascript
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Fügen Sie die Variable **connectionString** hinzu, und verwenden Sie sie zum Erstellen einer **Client**-Instanz. Ersetzen Sie den Platzhalterwert `{yourDeviceConnectionString}` durch die zuvor kopierte Verbindungszeichenfolge.

    ```javascript
    var connectionString = '{yourDeviceConnectionString}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Fügen Sie die folgende Funktion zur Behandlung der **lockDoor**-Methode hinzu.

    ```javascript
    var onLockDoor = function(request, response) {

        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });

        console.log('Locking Door!');
    };
    ```

7. Fügen Sie den folgenden Code hinzu, um den Handler für die **lockDoor**-Methode zu registrieren.

   ```javascript
   client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
   });
   ```

8. Speichern und schließen Sie die Datei **simDevice.js**.

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
>

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planen von Aufträgen zum Aufrufen einer direkten Methode und Aktualisieren der Eigenschaften eines Gerätezwillings

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, mit der eine **lockDoor**-Remotefunktion auf einem Gerät mit einer direkten Methode initiiert wird, und aktualisieren die Eigenschaften des Gerätezwillings.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **scheduleJobService**.  Erstellen Sie im Ordner **scheduleJobService** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen.  Übernehmen Sie alle Standardeinstellungen:

    ```console
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **scheduleJobService** den folgenden Befehl aus, um das Geräte-SDK-Paket **azure-iothub** und das Paket **azure-iot-device-mqtt** zu installieren:

    ```console
    npm install azure-iothub uuid --save
    ```

3. Erstellen Sie mit einem Text-Editor die neue Datei **scheduleJobService.js** im Ordner **scheduleJobService**.

4. Fügen Sie am Anfang der Datei **scheduleJobService.js** die folgenden ‚require‘-Anweisungen hinzu:

    ```javascript
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Fügen Sie die folgenden Variablendeklarationen hinzu. Ersetzen Sie den Platzhalterwert `{iothubconnectionstring}` durch den Wert, den Sie in [IoT Hub-Verbindungszeichenfolge abrufen](#get-the-iot-hub-connection-string) zuvor kopiert haben. Wenn Sie ein anderes Gerät als **myDeviceId** registriert haben, müssen Sie es in der Abfragebedingung unbedingt ändern.

    ```javascript
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Fügen Sie die folgende Funktion hinzu, die zum Überwachen der Auftragsausführung verwendet werden soll:

    ```javascript
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Fügen Sie den folgenden Code zum Planen des Auftrags hinzu, mit dem die Gerätemethode aufgerufen wird:
  
    ```javascript
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

8. Fügen Sie den folgenden Code zum Planen des Auftrags hinzu, mit dem der Gerätezwilling aktualisiert wird:

    ```javascript
    var twinPatch = {
       etag: '*',
       properties: {
           desired: {
               building: '43',
               floor: 3
           }
       }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```

9. Speichern und schließen Sie die Datei **scheduleJobService.js**.

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an der Eingabeaufforderung im Ordner **simDevice** den folgenden Befehl aus, um mit dem Lauschen auf die direkte Methode zum Neustarten zu beginnen.

    ```console
    node simDevice.js
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **scheduleJobService** den folgenden Befehl aus, um die Aufträge zum Absperren der Tür und zum Aktualisieren des Zwillings auszulösen.

    ```console
    node scheduleJobService.js
    ```

3. Die Reaktion des Geräts auf die direkte Methode und den Auftragsstatus wird in der-Konsole angezeigt.

   Das folgende Beispiel zeigt die Reaktion des Geräts auf die direkte Methode:

   ![Ausgabe der simulierten Geräte-App](./media/iot-hub-node-node-schedule-jobs/sim-device.png)

   Das folgende Beispiel zeigt die Dienstplanungsaufträge für die direkte Methode und die Aktualisierung des Gerätezwillings sowie die Aufträge, die bis zum Abschluss ausgeführt werden:

   ![Ausführen der simulierten Geräte-App](./media/iot-hub-node-node-schedule-jobs/schedule-job-service.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Auftrag zum Planen einer direkten Methode für ein Gerät und eines Updates der Eigenschaften eines Gerätezwillings verwendet.

Informationen zu den weiteren ersten Schritten mit IoT Hub und Geräteverwaltungsmustern wie z. B. einem imagebasierten End-to-End-Update finden Sie im [Tutorial „Device Update for Azure IoT Hub unter Verwendung des Raspberry Pi 3 B+-Referenzimages“](../iot-hub-device-update/device-update-raspberry-pi.md).

Informationen zu den weiteren ersten Schritten mit IoT Hub finden Sie unter [Erste Schritte mit Azure IoT Edge](../iot-edge/quickstart-linux.md).