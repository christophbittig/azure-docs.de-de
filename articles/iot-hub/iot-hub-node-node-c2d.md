---
title: C2D-Nachrichten mit Azure IoT Hub (Node) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Node.js C2D-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 75473561ecb827f12aeb02cb69b12259862da10b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229656"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>Senden von C2D-Nachrichten mit IoT Hub (Node.js)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. In [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erfahren Sie, wie Sie einen IoT-Hub erstellen, eine Geräteidentität darin bereitstellen und eine simulierte Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) auf. Es beschreibt Folgendes:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.
* Empfangen von C2D-Nachrichten auf einem Gerät.
* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu Cloud-zu-Gerät-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Node.js-Konsolen-Apps aus:

* **SimulatedDevice** ist eine modifizierte Version der App, die in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (C#)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erstellt wurde. Sie stellt eine Verbindung mit Ihrem IoT-Hub her und empfängt C2D-Nachrichten.

* **SendCloudToDeviceMessage** sendet über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App und empfängt die zugehörige Übermittlungsbestätigung.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java, Python und JavaScript). Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.
>

## <a name="prerequisites"></a>Voraussetzungen

* Node.js, Version 10.0.x oder höher. Unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) (Vorbereiten Ihrer Entwicklungsumgebung) wird beschrieben, wie Sie Node.js für dieses Tutorial unter Windows oder Linux installieren.

* Ein aktives Azure-Konto. (Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) erstellen.)

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt ändern Sie die simulierte Geräte-App, die Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Java)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erstellt haben, um C2D-Nachrichten vom IoT-Hub zu empfangen.

1. Öffnen Sie die Datei **SimulatedDevice.js** mit einem Text-Editor. Diese Datei befindet sich im Ordner **iot-hub\Quickstarts\simulated-device** aus dem Stammordner des Beispielcodes „Node. js“, den Sie im Schnellstart [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) heruntergeladen haben.

2. Registrieren Sie einen Handler beim Geräteclient, um von IoT Hub gesendete Nachrichten zu empfangen. Fügen Sie den Aufruf von `client.on` direkt nach der Zeile zum Erstellen des Geräteclients wie im folgenden Codeausschnitt hinzu:

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

In diesem Beispiel ruft das Gerät die Funktion **complete** zur Benachrichtigung von IoT Hub auf, dass die Nachricht verarbeitet wurde und aus der Gerätewarteschlange sicher entfernt werden kann. Der Aufruf von **complete** ist nicht erforderlich, wenn Sie den MQTT-Transport verwenden. Er kann ausgelassen werden. Für AMQP und HTTPS ist er erforderlich.

Mit AMQP und HTTPS, aber nicht MQTT, kann das Gerät auch folgende Aktionen ausführen:

* Eine Nachricht vorübergehend verwerfen, sodass IoT Hub sie für zukünftige Nutzung in der Gerätewarteschlange beibehält.
* Eine Nachricht ablehnen, wodurch sie aus der Warteschlange dauerhaft entfernt wird.

Wenn etwas passiert, wodurch verhindert wird, dass das Gerät die Nachricht abschließt, vorübergehend verwirft oder ablehnt, stellt IoT Hub sie nach einem festgelegten Zeitlimit zur erneuten Übermittlung in die Warteschlange. Aus diesem Grund muss die Nachrichtenverarbeitungslogik in der Geräte-App *idempotent* sein, sodass der mehrmalige Empfang derselben Nachricht dasselbe Ergebnis erzeugt.

Ausführlichere Informationen dazu, wie IoT Hub C2D-Nachrichten verarbeitet, einschließlich Details zum Lebenszyklus von C2D-Nachrichten, finden Sie unter [Senden von C2D-Nachrichten von einem IoT-Hub](iot-hub-devguide-messages-c2d.md).
  
> [!NOTE]
> Wenn Sie statt MQTT oder AMQP den HTTPS-Transport verwenden, prüft die **DeviceClient**-Instanz nur selten (mindestens alle 25 Minuten), ob Nachrichten von IoT Hub vorliegen. Weitere Informationen zu den Unterschieden zwischen der Unterstützung für MQTT, AMQP und HTTPS finden Sie unter [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md) und [Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, um C2D-Nachrichten über die IoT Hub-Instanz zu senden, die Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erstellt haben. Damit Ihr Dienst Cloud-zu-Gerät-Nachrichten senden kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Node.js-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) erstellt haben. Außerdem benötigen Sie die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

1. Erstellen Sie einen neuen leeren Ordner mit dem Namen **sendcloudtodevicemessage**. Erstellen Sie im Ordner **sendcloudtodevicemessage** die Datei „package.json“, indem Sie an der Eingabeaufforderung den unten angegebenen Befehl ausführen. Übernehmen Sie alle Standardeinstellungen:

    ```shell
    npm init
    ```

2. Führen Sie an der Eingabeaufforderung im Ordner **sendcloudtodevicemessage** den folgenden Befehl aus, um das Paket **azure-iothub** zu installieren:

    ```shell
    npm install azure-iothub --save
    ```

3. Erstellen Sie mithilfe eines Text-Editors die Datei **SendCloudToDeviceMessage.js** im Ordner **sendcloudtodevicemessage**.

4. Fügen Sie am Anfang der Datei **SendCloudToDeviceMessage.js** die folgenden `require`-Anweisungen hinzu:

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Fügen Sie der Datei **SendCloudToDeviceMessage.js** den folgenden Code hinzu. Ersetzen Sie die Platzhalterwerte „{IoTHubConnectionString}“ und „{deviceId}“ durch die IoT-Hub-Verbindungszeichenfolge bzw. durch die Geräte-ID, die Sie zuvor notiert haben:

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Fügen Sie die folgende Funktion hinzu, um Ergebnisse des Vorgangs an der Konsole auszugeben:

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Fügen Sie die folgende Funktion hinzu, um Übermittlungsfeedbacknachrichten an der Konsole auszugeben:

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Fügen Sie den folgenden Code hinzu, um eine Nachricht an Ihr Gerät zu senden und die Feedbacknachricht zu verarbeiten, wenn das Gerät die C2D-Nachricht bestätigt:

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. Speichern und schließen Sie die Datei **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie im Ordner **simulated-device** an der Eingabeaufforderung den folgenden Befehl aus, um Telemetriedaten an IoT Hub zu senden und auf C2D-Nachrichten zu lauschen:

    ```shell
    node SimulatedDevice.js
    ```

    ![Ausführen der simulierten Geräte-App](./media/iot-hub-node-node-c2d/receivec2d.png)

2. Führen Sie im Ordner **sendcloudtodevicemessage** an einer Eingabeaufforderung den folgenden Befehl aus, um eine C2D-Nachricht zu senden, und warten Sie auf das Bestätigungsfeedback:

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![Ausführen der App zum Senden des C2D-Befehls](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
   >

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden.

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).