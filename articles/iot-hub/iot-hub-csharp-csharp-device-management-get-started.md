---
title: Erste Schritte mit der Azure IoT Hub-Geräteverwaltung (.NET/.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe der Azure IoT Hub-Geräteverwaltung den Neustart eines Remotegeräts initiieren. Sie verwenden das Azure IoT-Geräte-SDK für .NET, um eine simulierte Geräte-App zu implementieren, die eine direkte Methode enthält, und das Azure IoT-Dienst-SDK für .NET, um eine Dienst-App zu implementieren, die die direkte Methode aufruft.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: 750dd2c88e826ac42d7806b1ad0d210ede232196
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551833"
---
# <a name="get-started-with-device-management-net"></a>Erste Schritte mit der Geräteverwaltung (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Verwenden des Azure-Portals zum Erstellen eines IoT Hubs und einer Geräteidentität im IoT Hub

* Erstellen Sie eine simulierte Geräte-App, die eine direkte Methode enthält, die das Gerät neu startet. Direkte Methoden werden aus der Cloud aufgerufen.

* Erstellen einer .NET-Konsolenanwendung, die über Ihre IoT Hub-Instanz eine direkte Neustartmethode in der simulierten Geräte-App aufruft

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps:

* **SimulateManagedDevice**. Diese App stellt mit der zuvor erstellten Geräteidentität eine Verbindung mit Ihrem IoT-Hub her, empfängt eine direkte Methode zum Neustart, simuliert einen physischen Neustart und meldet den Zeitpunkt des letzten Neustarts.

* **TriggerReboot**. Diese App ruft eine direkte Methode in der simulierten Geräte-App auf und zeigt die Antwort sowie die aktualisierten gemeldeten Eigenschaften an.

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Auslösen eines Remoteneustarts auf dem Gerät über eine direkte Methode

In diesem Abschnitt erstellen Sie (mit C#) eine .NET Konsolen-App, die über eine direkte Methode einen Remoteneustart auf einem Gerät auslöst. Die App verwendet Gerätezwillingsabfragen, um den Zeitpunkt des letzten Neustarts bei diesem Gerät zu ermitteln.

1. Wählen Sie in Visual Studio **Neues Projekt erstellen** aus.

1. Suchen Sie in **Neues Projekt erstellen** die Projektvorlage **Konsolen-App (.NET Framework)** , wählen Sie sie und dann **Weiter** aus.

1. Nennen Sie in **Neues Projekt konfigurieren** das Projekt *TriggerReboot*, und wählen Sie .NET Framework, Version 4.5.1 oder höher, aus. Klicken Sie auf **Erstellen**.

    ![Neues Visual C#-Projekt für den klassischen Windows-Desktop](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **TriggerReboot**, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Wählen Sie **Durchsuchen** aus, suchen Sie nach dem Paket **Microsoft.Azure.Devices**, und wählen Sie es aus. Wählen Sie **Installieren** aus, um das Paket **Microsoft.Azure.Devices** zu installieren.

    ![Fenster „NuGet-Paket-Manager“](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   In diesem Schritt wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert `{iot hub connection string}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Fügen Sie der **Program**-Klasse die folgende Methode hinzu.  Dieser Code ruft den Gerätezwilling für das neu startende Gerät auf und gibt die gemeldeten Eigenschaften aus.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Fügen Sie der **Program**-Klasse die folgende Methode hinzu.  Dieser Code löst den Neustart auf dem Gerät mit einer direkten Methode aus.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Wählen Sie **Erstellen** > **Projektmappe erstellen** aus.

> [!NOTE]
> In diesem Tutorial wird nur eine einzelne Abfrage für die gemeldeten Eigenschaften des Geräts durchgeführt. Der Produktionscode sollte einen Abruf enthalten, um die Änderungen in den gemeldeten Eigenschaften des Geräts zu erkennen.

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt führen Sie folgende Schritte aus:

* Erstellen einer .NET-Konsolen-App, die auf eine von der Cloud aufgerufene direkte Methode antwortet

* Auslösen des Neustarts eines simulierten Geräts

* Verwenden der gemeldeten Eigenschaften, um Gerätezwillingsabfragen zu ermöglichen, die Geräte und den Zeitpunkt ihres letzten Neustarts ermitteln

Führen Sie zum Erstellen der simulierten Geräte-App die folgenden Schritte aus:

1. Wählen Sie in Visual Studio in der bereits erstellten Projektmappe „TriggerReboot“ **Datei** > **Neu** > **Projekt** aus. Suchen Sie in **Neues Projekt erstellen** die Projektvorlage **Konsolen-App (.NET Framework)** , wählen Sie sie und dann **Weiter** aus.

1. Nennen Sie in **Neues Projekt konfigurieren** das Projekt *SimulateManagedDevice*, und wählen Sie für **Projektmappe** die Option **Zur Projektmappe hinzufügen** aus. Klicken Sie auf **Erstellen**.

    ![Ihr Projekt benennen und es der Projektmappe hinzufügen](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das neue Projekt **SimulateManagedDevice**, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Wählen Sie **Durchsuchen** aus, suchen Sie nach dem Paket **Microsoft.Azure.Devices.Client**, und wählen Sie es aus. Wählen Sie **Installieren** aus.

    ![Fenster „NuGet-Paket-Manager“ mit Client-App](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   In diesem Schritt wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert sowie ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert `{device connection string}` durch die Geräteverbindungszeichenfolge, die Sie weiter oben im Abschnitt [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) notiert haben.

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Fügen Sie Folgendes hinzu, um die direkte Methode auf dem Gerät zu implementieren:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Fügen Sie abschließend der **Main**-Methode folgenden Code hinzu, um die Verbindung mit dem IoT Hub zu öffnen und den Listener der Methode zu initialisieren:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus.

1. Wählen Sie für **Allgemeine Eigenschaften** > **Startprojekt** die Option **Einzelnes Startprojekt** und dann das Projekt **SimulateManagedDevice** aus. Klicken Sie zum Speichern der Änderungen auf **OK** .

1. Wählen Sie **Erstellen** > **Projektmappe erstellen** aus.

> [!NOTE]
> Der Einfachheit halber wird in diesem Tutorial keine Wiederholungsrichtlinie implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z.B. exponentielles Backoff), wie es in [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) vorgeschlagen wird.

## <a name="run-the-apps"></a>Ausführen der Apps

Jetzt können Sie die Apps ausführen.

1. Um die .NET-Geräte-App **SimulateManagedDevice** auszuführen, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateManagedDevice**, wählen Sie **Debuggen** und dann **Neue Instanz starten** aus. Die App sollte mit dem Lauschen auf die Methodenaufrufe aus Ihrem IoT-Hub beginnen.

1. Nachdem das Gerät verbunden wurde und auf Methodenaufrufe wartet, klicken Sie mit der rechten Maustaste auf das Projekt **TriggerReboot**, wählen Sie **Debuggen** und dann **Neue Instanz starten** aus.

   „Neustart wird ausgeführt!“ sollte auf der **SimulatedManagedDevice**-Konsole angezeigt werden, und die gemeldeten Eigenschaften des Geräts, darunter der Zeitpunkt des letzten Neustarts, sollten auf der **TriggerReboot**-Konsole angezeigt werden.

    ![Ausführen der Dienst- und Geräte-App](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
