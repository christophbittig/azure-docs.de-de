---
title: C2D-Nachrichten mit Azure IoT Hub (Java) | Microsoft Docs
description: Erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für Java C2D-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden. Sie passen eine simulierte Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: 1493e9166e9d9b1584187fe6d7210936db7cc038
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234431"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-java"></a>Senden von C2D-Nachrichten mit IoT Hub (Java)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. In [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) erfahren Sie, wie Sie einen IoT-Hub erstellen, eine Geräteidentität darin bereitstellen und eine simulierte Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) auf. Sie erfahren darin, wie Sie Folgendes ausführen:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu C2D-Nachrichten finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei Java-Konsolen-Apps aus:

* **simulated-device** ist eine modifizierte Version der App, die in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Java)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) erstellt wurde. Sie stellt eine Verbindung mit Ihrem IoT-Hub her und empfängt C2D-Nachrichten.

* **send-c2d-messages** sendet über IoT Hub eine C2D-Nachricht an die simulierte Geräte-App und empfängt die zugehörige Übermittlungsbestätigung.

> [!NOTE]
> IoT Hub bietet durch Azure IoT-Geräte-SDKs Unterstützung für zahlreiche Geräteplattformen und Sprachen (u.a. C, Java, Python und JavaScript). Im [Azure IoT Developer Center](https://azure.microsoft.com/develop/iot) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.

## <a name="prerequisites"></a>Voraussetzungen

* Eine vollständige, funktionierende Version der Schnellstartanleitung [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Java)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) oder des Tutorials [Verwenden der Azure CLI und des Azure-Portals zum Konfigurieren des IoT Hub-Nachrichtenroutings](tutorial-routing.md).

* [Java SE Development Kit 8](/java/azure/jdk/) Wählen Sie unter **Langfristiger Support** unbedingt **Java 8** aus, um zu den Downloads für JDK 8 zu gelangen.

* [Maven 3](https://maven.apache.org/download.cgi)

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Empfangen von Nachrichten in der simulierten Geräte-App

In diesem Abschnitt ändern Sie die simulierte Geräte-App, die Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Java)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) erstellt haben, um C2D-Nachrichten vom IoT-Hub zu empfangen.

1. Öffnen Sie die Datei „simulated-device\src\main\java\com\mycompany\app\App.java“ mit einem Text-Editor.

2. Fügen Sie die folgende **MessageCallback**-Klasse als geschachtelte Klasse innerhalb der **App**-Klasse hinzu. Die **execute**-Methode wird aufgerufen, wenn das Gerät eine Nachricht von IoT Hub empfängt. In diesem Beispiel informiert das Gerät den IoT-Hub immer darüber, dass die Nachricht abgeschlossen wurde:

    ```java
    private static class AppMessageCallback implements MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Ändern Sie die **main**-Methode so, dass vor dem Öffnen des Clients eine **AppMessageCallback**-Instanz erstellt und die **SetMessageCallback**-Methode aufgerufen wird:

    ```java
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new AppMessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

4. Führen Sie zum Erstellen der App **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

Die Methode `execute` in der Klasse `AppMessageCallback` gibt `IotHubMessageResult.COMPLETE` zurück. Dadurch wird IoT Hub informiert, dass die Nachricht erfolgreich verarbeitet wurde und aus der Gerätewarteschlange sicher entfernt werden kann. Das Gerät sollte diesen Wert zurückgeben, wenn seine Verarbeitung – unabhängig vom verwendeten Protokoll – erfolgreich abgeschlossen wurde.

Mit AMQP und HTTPS, aber nicht MQTT, kann das Gerät auch folgende Aktionen ausführen:

* Eine Nachricht vorübergehend verwerfen, sodass IoT Hub sie für zukünftige Nutzung in der Gerätewarteschlange beibehält.
* Eine Nachricht ablehnen, wodurch sie aus der Warteschlange dauerhaft entfernt wird.

Wenn etwas passiert, wodurch verhindert wird, dass das Gerät die Nachricht abschließt, vorübergehend verwirft oder ablehnt, stellt IoT Hub sie nach einem festgelegten Zeitlimit zur erneuten Übermittlung in die Warteschlange. Aus diesem Grund muss die Nachrichtenverarbeitungslogik in der Geräte-App *idempotent* sein, sodass der mehrmalige Empfang derselben Nachricht dasselbe Ergebnis erzeugt.

Ausführlichere Informationen dazu, wie IoT Hub C2D-Nachrichten verarbeitet, einschließlich Details zum Lebenszyklus von C2D-Nachrichten, finden Sie unter [Senden von C2D-Nachrichten von einem IoT-Hub](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Wenn Sie statt MQTT oder AMQP den HTTPS-Transport verwenden, prüft die **DeviceClient**-Instanz nur selten (mindestens alle 25 Minuten), ob Nachrichten von IoT Hub vorliegen. Weitere Informationen zu den Unterschieden zwischen der Unterstützung für MQTT, AMQP und HTTPS finden Sie unter [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md) und [Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md).

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, um Cloud-zu-Gerät-Nachrichten über den IoT-Hub zu senden, den Sie unter [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Node.js)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) erstellt haben. Damit Ihr Dienst Cloud-zu-Gerät-Nachrichten senden kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine Java-Konsolen-App, die C2D-Nachrichten an die simulierte Geräte-App sendet. Sie benötigen die Geräte-ID des Geräts, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) erstellt haben. Außerdem benötigen Sie die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

1. Erstellen Sie mithilfe des folgenden Befehls über die Eingabeaufforderung ein Maven-Projekt namens **send-c2d-messages**. Beachten Sie, dass es sich hierbei um einen einzelnen langen Befehl handelt:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Navigieren Sie an der Eingabeaufforderung zu dem neuen Ordner „send-c2d-messages“.

3. Öffnen Sie die Datei „pom.xml“ aus dem Ordner „send-c2d-messages“ in einem Text-Editor, und fügen Sie dem Knoten **dependencies** die folgende Abhängigkeit hinzu. Durch Hinzufügen der Abhängigkeit können Sie in Ihrer Anwendung das Paket **iothub-java-service-client** verwenden, um mit Ihrem IoT Hub-Dienst zu kommunizieren:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Sie finden die aktuelle Version von **iot-service-client** mithilfe der [Maven-Suche](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Speichern und schließen Sie die Datei „pom.xml“.

5. Öffnen Sie die Datei „send-c2d-messages\src\main\java\com\mycompany\app\App.java“ in einem Text-Editor.

6. Fügen Sie der Datei die folgenden **import** -Anweisungen hinzu:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Fügen Sie der Klasse **App** die folgenden Klassenebenenvariablen hinzu, und ersetzen Sie dabei **{yourhubconnectionstring}** und **{yourdeviceid}** durch die zuvor notierten Werte:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol =    
        IotHubServiceClientProtocol.AMQPS;
    ```

8. Ersetzen Sie die **main**-Methode durch den folgenden Code. Dieser Code stellt die Verbindung mit Ihrer IoT Hub-Instanz her, sendet eine Nachricht an Ihr Gerät und wartet dann auf eine Empfangs- und Verarbeitungsbestätigung des Geräts:

    ```java
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver();
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [!NOTE]
    > Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es im Artikel [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.

9. Führen Sie zum Erstellen der App **simulated-device** mit Maven den folgenden Befehl an der Eingabeaufforderung im Ordner „simulated-device“ aus:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Ausführen der Anwendungen

Sie können nun die Anwendungen ausführen.

1. Führen Sie an einer Befehlszeile im Ordner „simulated-device“ den folgenden Befehl aus, um mit dem Senden von Telemetriedaten an Ihren IoT Hub und Empfangen von C2D-Nachrichten, die von Ihrem Hub gesendet werden, zu beginnen:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ausführen der simulierten Geräte-App](./media/iot-hub-java-java-c2d/receivec2d.png)

2. Führen Sie an einer Befehlszeile im Ordner „send-c2d-messages“ den folgenden Befehl aus, um eine C2D-Nachricht zu senden, und warten Sie auf eine Feedbackbestätigung:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Ausführen des Befehls zum Senden der C2D-Nachricht](media/iot-hub-java-java-c2d/sendc2d.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie Cloud-zu-Gerät-Nachrichten gesendet und empfangen werden.

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).