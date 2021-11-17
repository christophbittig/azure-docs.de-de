---
title: Planen von Aufträgen mit Azure IoT Hub (.NET/.NET) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Azure IoT Hub-Auftrag planen, um eine direkte Methode auf mehreren Geräten aufzurufen. Sie verwenden die Azure IoT-Geräte-SDKs für .NET, um die simulierten Geräte-Apps und eine Dienst-App für die Auftragsausführung zu implementieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: d1dbbcf85c50bb0e21d1dea2b5f393236185dcd3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556057"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Planen und Übertragen von Aufträgen (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Verwenden Sie Azure IoT Hub zum Planen und Nachverfolgen von Aufträgen, die Millionen von Geräte aktualisieren. Verwenden Sie Aufträge zum:

* Aktualisieren gewünschter Eigenschaften

* Aktualisieren von Tags

* Aufrufen direkter Methoden

Ein Auftrag umschließt eine dieser Aktionen und verfolgt die Ausführung für eine Gruppe von Geräten nach, die anhand einer Gerätezwillingsabfrage definiert wird. Eine Back-End-App kann z.B. einen Auftrag verwenden, um eine direkte Methode auf 10.000 Geräten aufzurufen, die die Geräte neu startet. Sie geben die Gruppe von Geräten mit einer Gerätezwillingsabfrage ein und planen die Ausführung des Auftrags zu einem späteren Zeitpunkt. Der Auftrag verfolgt den Fortschritt nach, während jedes der Geräte die Neustart-Direktmethode empfängt und ausführt.

Weitere Informationen zu diesen Funktionen finden Sie unter:

* Gerätezwillinge und Eigenschaften: [Erste Schritte mit Gerätezwillingen](iot-hub-csharp-csharp-twin-getstarted.md) und [Tutorial: Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)

* Direkte Methoden: [IoT Hub-Entwicklerhandbuch – direkte Methoden](iot-hub-devguide-direct-methods.md) und [Schnellstart: Verwenden von direkten Methoden](./quickstart-control-device.md?pivots=programming-language-csharp)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

* Erstellen Sie eine Geräte-App, die eine direkte Methode namens **LockDoor** implementiert, die von der Back-End-App aufgerufen werden kann.

* Erstellen Sie eine Back-End-App, die einen Auftrag zum Aufrufen der direkten **LockDoor**-Methode auf mehreren Geräten erstellt. Ein anderer Auftrag sendet die Updates der gewünschten Eigenschaft an mehrere Geräte.

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps (C#):

* **SimulateDeviceMethods:** Diese App verbindet Ihren IoT-Hub und implementiert die direkte **LockDoor**-Methode.

* **ScheduleJob:** Diese App verwendet Aufträge zum Aufrufen der direkten **LockDoor**-Methode und Aktualisieren der gewünschten Gerätezwillingseigenschaften auf mehreren Geräten.

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Erstellen einer simulierten Geräte-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, die auf eine vom Lösungs-Back-End aufgerufene direkte Methode antwortet.

1. Wählen Sie in Visual Studio **Neues Projekt erstellen** und dann die Projektvorlage **Konsolen-App (.NET Framework)** aus. Klicken Sie auf **Weiter**, um fortzufahren.

1. Geben Sie dem Projekt unter **Neues Projekt konfigurieren** den Namen *SimulateDeviceMethods*, und wählen Sie **Erstellen** aus.

    ![Konfigurieren des Projekts „SimulateDeviceMethods“](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateDeviceMethods**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

1. Wählen Sie im **NuGet-Paket-Manager** die Option **Durchsuchen** aus, suchen Sie nach dem Paket **Microsoft.Azure.Devices.Client**, und wählen Sie es aus. Wählen Sie **Installieren** aus.

    ![Fenster „NuGet-Paket-Manager“ mit Client-App](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    In diesem Schritt wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert sowie ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Geräteverbindungszeichenfolge, die Sie sich im vorherigen Abschnitt notiert haben:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Fügen Sie den folgenden Code hinzu, um die direkte Methode auf dem Gerät zu implementieren:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Fügen Sie die folgende Methode hinzu, um den Listener für Gerätezwillinge auf dem Gerät zu implementieren:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Fügen Sie abschließend der **Main**-Methode folgenden Code hinzu, um die Verbindung mit dem IoT Hub zu öffnen und den Listener der Methode zu initialisieren:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Speichern Sie Ihre Arbeit, und erstellen Sie Ihre Lösung.

> [!NOTE]
> Der Einfachheit halber werden in diesem Tutorial keine Wiederholungsrichtlinien implementiert. Im Produktionscode sollten Sie Wiederholungsrichtlinien implementieren (z. B. einen erneuten Verbindungsversuch), wie unter [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults) beschrieben.
>

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Planen von Aufträgen zum Aufrufen einer direkten Methode und Senden der Gerätezwillingsupdates

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App (mithilfe von C#), die Aufträge zum Aufrufen der direkten **LockDoor**-Methode und Senden der Aktualisierungen der gewünschten Eigenschaft an mehrere Geräte verwendet.

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**. Wählen Sie unter **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie dem Projekt unter **Neues Projekt konfigurieren** den Namen *ScheduleJob*. Wählen Sie für **Lösung** die Option **Zur Projektmappe hinzufügen** aus, und wählen Sie dann **Erstellen** aus.

    ![Benennen und Konfigurieren des Projekts „ScheduleJob“](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ScheduleJob**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

1. Wählen Sie im **NuGet-Paket-Manager** die Option **Durchsuchen** aus, suchen Sie nach **Microsoft.Azure.Devices**, und wählen Sie diese Option und dann **Installieren** aus.

   In diesem Schritt wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Fügen Sie die folgende `using`-Anweisung in den Standardanweisungen hinzu, sofern sie noch nicht vorhanden ist.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie die Platzhalter durch die IoT-Hub-Verbindungszeichenfolge, die Sie zuvor unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben, bzw. durch den Namen Ihres Geräts.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Fügen Sie der **Program**-Klasse eine weitere Methode hinzu:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Weitere Informationen zur Abfragesyntax finden Sie unter [IoT Hub-Abfragesprache](./iot-hub-devguide-query-language.md).
    >

1. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Speichern Sie Ihre Arbeit, und erstellen Sie Ihre Lösung.

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen.

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie dann **Startprojekte festlegen** aus.

1. Wählen Sie **Allgemeine Eigenschaften** > **Startprojekt** und dann **Mehrere Startprojekte** aus.

1. Stellen Sie sicher, dass `SimulateDeviceMethods` ganz oben in der Liste angezeigt wird, gefolgt von `ScheduleJob`. Legen Sie die Aktionen für beide auf **Starten** fest, und wählen Sie **OK** aus.

1. Führen Sie die Projekte aus, indem Sie auf **Starten** klicken oder in das Menü **Debuggen** wechseln und auf **Debugging starten** klicken.

   Sie sehen die Ausgabe von Geräte- und Back-End-App.

    ![Führen Sie die Apps aus, um Aufträge zu planen.](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Auftrag zum Planen einer direkten Methode für ein Gerät und eines Updates der Eigenschaften eines Gerätezwillings verwendet.

* Informationen zu den weiteren ersten Schritten mit IoT Hub und Geräteverwaltungsmustern wie z. B. einem imagebasierten End-to-End-Update finden Sie im [Tutorial „Device Update for Azure IoT Hub unter Verwendung des Raspberry Pi 3 B+-Referenzimages“](../iot-hub-device-update/device-update-raspberry-pi.md).

* Informationen zum Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge finden Sie unter [Schnellstart: Bereitstellen des ersten IoT Edge-Moduls auf einem Linux-basierten x64-Gerät](../iot-edge/quickstart-linux.md).