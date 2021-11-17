---
title: C2D-Nachrichten mit Azure IoT Hub (.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT SDKs für .NET C2D-Nachrichten von einer Azure IoT Hub-Instanz an ein Gerät senden. Sie passen eine Geräte-App für den Empfang von C2D-Nachrichten und eine Back-End-App zum Senden der C2D-Nachrichten an.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: lizross
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: 925900409bb9f626d25359d2c809398c22baa408
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553893"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Senden von Nachrichten aus der Cloud an das Gerät mit IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. In der Schnellstartanleitung [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (C#)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) erfahren Sie, wie Sie einen IoT-Hub erstellen, eine Geräteidentität darin bereitstellen und eine Geräte-App programmieren, die D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud) sendet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial baut auf [Schnellstart: Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz und Lesen der Telemetriedaten aus der IoT Hub-Instanz mit einer Back-End-Anwendung (Python)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) auf. Es zeigt, wie Sie die folgenden Aufgaben ausführen:

* Senden von C2D-Nachrichten aus Ihrem Lösungs-Back-End an ein einzelnes Gerät über IoT Hub.

* Empfangen von C2D-Nachrichten auf einem Gerät.

* Anfordern einer Übermittlungsbestätigung (*Feedback*) von Ihrem Lösungs-Back-End für Nachrichten, die von IoT Hub an ein einzelnes Gerät gesendet wurden.

Weitere Informationen zu C2D-Nachrichten finden Sie unter [D2C- und C2D-Messaging IoT Hub](iot-hub-devguide-messaging.md).

Am Ende dieses Tutorials führen Sie zwei .NET-Konsolen-Apps aus.

* **SimulatedDevice**. Diese App stellt eine Verbindung mit Ihrem IoT-Hub her und empfängt C2D-Nachrichten. Bei dieser App handelt es sich um eine geänderte Version der App, die in [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) erstellt wurde.

* **SendCloudToDevice**. Diese App sendet über IoT Hub eine C2D-Nachricht an die Geräte-App und empfängt die zugehörige Übermittlungsbestätigung.

> [!NOTE]
> IoT Hub bietet durch [Azure IoT-Geräte-SDKs](iot-hub-devguide-sdks.md) Unterstützung für zahlreiche Geräteplattformen und Sprachen, darunter C, Java, Python und JavaScript. Im [IoT Hub-Entwicklerhandbuch](iot-hub-devguide.md) finden Sie Schritt-für-Schritt-Anweisungen zum Verbinden eines Geräts mit dem Code in diesem Tutorial sowie allgemeine Informationen zum Verbinden mit Azure IoT Hub.
>

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-device-app"></a>Empfangen von Nachrichten in der Geräte-App

In diesem Abschnitt ändern Sie die Geräte-App, die Sie in [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) erstellt haben, um C2D-Nachrichten vom IoT-Hub zu empfangen.

1. Fügen Sie in Visual Studio im **SimulatedDevice**-Projekt der **SimulatedDevice**-Klasse die folgende Methode hinzu.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. Fügen Sie in der **Main**-Methode unmittelbar vor der Zeile `Console.ReadLine()` die folgende Methode hinzu:

   ```csharp
   ReceiveC2dAsync();
   ```

Die `ReceiveAsync`-Methode gibt die empfangene Nachricht asynchron zurück, sobald sie vom Gerät empfangen wird. Sie gibt nach einem spezifizierbaren Zeitlimit *NULL* zurück. In diesem Beispiel wird der Standardwert von einer Minute verwendet. Wenn die App *NULL* empfängt, sollte sie weiter auf neue Nachrichten warten. Diese Anforderung ist der Grund für die `if (receivedMessage == null) continue`-Zeile.

Durch den Aufruf von `CompleteAsync()` wird IoT Hub darüber informiert, dass die Nachricht erfolgreich verarbeitet wurde und sicher aus der Gerätewarteschlange entfernt werden kann. Das Gerät sollte diese Methode unabhängig vom verwendeten Protokoll aufrufen, wenn seine Verarbeitung erfolgreich abgeschlossen wurde.

Mit AMQP und HTTPS, aber nicht MQTT, kann das Gerät auch folgende Aktionen ausführen:

* Eine Nachricht vorübergehend verwerfen, sodass IoT Hub sie für zukünftige Nutzung in der Gerätewarteschlange beibehält.
* Eine Nachricht ablehnen, wodurch sie aus der Warteschlange dauerhaft entfernt wird.

Wenn etwas passiert, wodurch verhindert wird, dass das Gerät die Nachricht abschließt, vorübergehend verwirft oder ablehnt, stellt IoT Hub sie nach einem festgelegten Zeitlimit zur erneuten Übermittlung in die Warteschlange. Aus diesem Grund muss die Nachrichtenverarbeitungslogik in der Geräte-App *idempotent* sein, sodass der mehrmalige Empfang derselben Nachricht dasselbe Ergebnis erzeugt.

Ausführlichere Informationen dazu, wie IoT Hub C2D-Nachrichten verarbeitet, einschließlich Details zum Lebenszyklus von C2D-Nachrichten, finden Sie unter [Senden von C2D-Nachrichten von einem IoT-Hub](iot-hub-devguide-messages-c2d.md).

> [!NOTE]
> Wird HTTPS anstelle von MQTT oder AMQP als Transport verwendet, erfolgt von der `ReceiveAsync`-Methode sofort eine Rückgabe. Das unterstützte Muster für Cloud-zu-Gerät-Nachrichten mit HTTPS ist zeitweilig mit Geräten verbunden, die selten Nachrichten abrufen (mindestens alle 25 Minuten). Die Ausgabe von mehr HTTPS-Empfangsvorgängen führt dazu, dass die Anforderungen von IoT Hub gedrosselt werden. Weitere Informationen zu den Unterschieden zwischen der Unterstützung für MQTT, AMQP und HTTPS finden Sie unter [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md) und [Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md).
>

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

In diesem Artikel erstellen Sie einen Back-End-Dienst, um C2D-Nachrichten über die IoT Hub-Instanz zu senden, die Sie in [Senden von Telemetriedaten von einem Gerät an eine IoT Hub-Instanz ](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) erstellt haben. Damit Ihr Dienst Cloud-zu-Gerät-Nachrichten senden kann, muss er über die Berechtigung **Dienstverbindung** verfügen. Standardmäßig wird jeder IoT-Hub mit einer SAS-Richtlinie namens **service** erstellt, die diese Berechtigung erteilt.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Senden einer C2D-Nachricht

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, die Cloud-zu-Gerät-Nachrichten an die simulierte Geräte-App sendet.

1. Wählen Sie in der aktuellen Visual Studio-Projektmappe **Datei** > **Neu** > **Projekt** aus. Wählen Sie in **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Nennen Sie das Projekt *SendCloudToDevice*. Wählen Sie unter **Projektmappe** die Option **Zur Projektmappe hinzufügen** aus, und übernehmen Sie die neueste Version von .NET Framework. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

   ![Konfigurieren eines neuen Projekts in Visual Studio](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die neue Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

1. Wählen Sie in **NuGet-Pakete verwalten** die Option **Durchsuchen**, anschließend „Suchen nach“ und dann **Microsoft.Azure.Devices** aus. Wählen Sie **Installieren** aus.

   In diesem Schritt wird das [NuGet-Paket mit dem Dienst-SDK für Azure IoT](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen, installiert und mit einem Verweis versehen.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgende `using`-Anweisung hinzu.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert `{iot hub connection string}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) notiert haben. Ersetzen Sie den Platzhalter `{device id}` durch die Geräte-ID des Geräts, das Sie in [Schnellstart: Senden von Telemetriedaten von einem Gerät an einen IoT-Hub und Lesen der Telemetriedaten mit einer Back-End-Anwendung (.NET)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) erstellt haben.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   static string targetDevice = "{device id}";
   ```

1. Fügen Sie der **Program**-Klasse die folgende Methode hinzu, um eine Nachricht an Ihr Gerät zu senden.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync(targetDevice, commandMessage);
   }
   ```

1. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus.

1. Klicken Sie unter **Allgemeine Eigenschaften** > **Startprojekt** auf die Option **Mehrere Startprojekte** und dann auf die Aktion **Starten** für **SimulatedDevice** und **SendCloudToDevice**. Klicken Sie zum Speichern der Änderungen auf **OK** .

1. Drücken Sie **F5**. Beide Anwendungen sollten gestartet werden. Wählen Sie das Fenster **SendCloudToDevice** aus, und drücken Sie die **EINGABETASTE**. Die von der Geräte-App empfangene Meldung sollte angezeigt werden.

   ![Geräte-App, die eine Nachricht empfängt](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Empfangen von Übermittlungsfeedback

Es ist möglich, für jede C2D-Nachricht Übermittlungsbestätigungen (oder Ablaufbestätigungen) von IoT Hub anzufordern. Mit dieser Option kann die Wiederholungs- oder Kompensierungslogik problemlos vom Lösungs-Back-End informiert werden. Weitere Informationen zu C2D-Feedback finden Sie unter [D2C- und C2D-Messaging mit IoT Hub](iot-hub-devguide-messaging.md).

In diesem Abschnitt ändern Sie die **SendCloudToDevice**-App so, dass sie Feedback von IoT Hub anfordert und empfängt.

1. Fügen Sie in Visual Studio im **SendCloudToDevice**-Projekt der **Program**-Klasse folgende Methode hinzu.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    Beachten Sie, dass das Empfangsmuster mit dem Muster zum Empfangen von Cloud-zu-Gerät-Nachrichten von der Geräte-App identisch ist.

1. Fügen Sie in der Methode **Main** die folgende Zeile direkt nach `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` hinzu.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. Um für die Übermittlung der C2D-Nachricht Feedback anzufordern, müssen Sie in der **SendCloudToDeviceMessageAsync** -Methode eine Eigenschaft angeben. Fügen Sie direkt nach der Zeile `var commandMessage = new Message(...);` die folgende Zeile hinzu.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. Drücken Sie **F5**, um die Apps auszuführen. Beide Anwendungen sollten nun gestartet werden. Wählen Sie das Fenster **SendCloudToDevice** aus, und drücken Sie die **EINGABETASTE**. Die Nachricht sollte von der Geräte-App empfangen werden, und nach einigen Sekunden sollte die Feedbacknachricht von der **SendCloudToDevice**-Anwendung empfangen werden.

   ![Geräte-App, die eine Nachricht empfängt, und Dienst-App, die Feedback erhält](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren, z.B. exponentielles Backoff, wie es in [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.
>

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie gelernt, wie C2D-Nachrichten gesendet und empfangen werden.

Weitere Informationen zum Entwickeln von Lösungen mit IoT Hub finden Sie im [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).