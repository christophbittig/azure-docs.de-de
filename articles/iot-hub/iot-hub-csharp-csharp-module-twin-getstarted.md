---
title: Erste Schritte mit der Modulidentität und dem Modulzwilling von Azure IoT Hub (.NET)
description: Hier erfahren Sie, wie Sie mit IoT SDKs für .NET eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: lizross
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 22fb3c9959f3f71df0b95b41bebb9ccc445815fd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555181"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts und geben Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen Modulidentitäten und Modulzwillinge isolierte Konfigurationen und Zustände für die einzelnen Komponenten.

Am Ende dieses Tutorials verfügen Sie über zwei .NET-Konsolen-Apps:

* **CreateIdentities:** Mit dieser App werden eine Geräteidentität, eine Modulidentität und ein zugehöriger Sicherheitsschlüssel zum Verbinden Ihrer Geräte- und Modulclients erstellt.

* **UpdateModuleTwinReportedProperties:** Mit dieser App werden aktualisierte vom Modulzwilling gemeldete Eigenschaften an Ihren IoT-Hub sendet.

> [!NOTE]
> Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

## <a name="create-a-hub"></a>Erstellen eines Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualisieren des Modulzwillings mithilfe des .NET-Geräte-SDKs

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App auf Ihrem simulierten Gerät, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert.

Rufen Sie vor dem Beginn die Modulverbindungszeichenfolge ab. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an. Navigieren Sie zu Ihrem Hub, und wählen Sie **IoT-Geräte** aus. Suchen Sie **myFirstDevice**. Wählen Sie **myFirstDevice** aus, um es zu öffnen, und wählen Sie dann **myFirstModule** aus, um es zu öffnen. Kopieren Sie bei Bedarf unter **Details zur Modulidentität** die **Verbindungszeichenfolge (Primärschlüssel)** im folgenden Verfahren.

   ![Moduldetails im Azure-Portal](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Fügen Sie Ihrer Projektmappe in Visual Studio ein neues Projekt hinzu, indem Sie **Datei** > **Neu** > **Projekt** auswählen. Wählen Sie in „Neues Projekt erstellen“ die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Nennen Sie das Projekt *UpdateModuleTwinReportedProperties*. Wählen Sie unter **Projektmappe** die Option **Zur Projektmappe hinzufügen** aus. Stellen Sie sicher, dass .NET Framework-Version 4.6.1 oder höher verwendet wird.

    ![Erstellen eines Visual Studio-Projekts](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Wählen Sie **Erstellen** aus, um das Projekt zu erstellen.

1. Navigieren Sie in Visual Studio zu **Tools** > **NuGet-Paket-Manager** > **NuGet-Pakete für Projektmappe verwalten**. Wählen Sie die Registerkarte **Durchsuchen** aus.

1. Suchen Sie nach **Microsoft.Azure.Devices.Client**, und wählen Sie diese Option aus. Wählen Sie dann **Installieren** aus.

    ![Der Screenshot zeigt die ausgewählte Option „Microsoft.Azure.Devices.Client“ mit hervorgehobener Schaltfläche „Installieren“.](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Verbindungszeichenfolge des Moduls.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Fügen Sie die folgende Methode **OnDesiredPropertyChanged** der Klasse **Program** hinzu:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. Fügen Sie der **Main**-Methode die folgenden Zeilen hinzu:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    In diesem Codebeispiel wird gezeigt, wie Sie den Modulzwilling abrufen und die gemeldeten Eigenschaften mit dem AMQP-Protokoll aktualisieren. In der öffentlichen Vorschau wird nur AMQP für Modulzwillingsvorgänge unterstützt.

1. Optional können Sie diese Anweisungen der **Main**-Methode hinzufügen, um ein Ereignis von Ihrem Modul an IoT Hub zu senden. Platzieren Sie diese Zeilen unterhalb des Blocks `try catch`.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Ausführen der Apps

Sie können die Apps nun ausführen.

1. Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus.

1. Wählen Sie unter **Allgemeine Eigenschaften** die Option **Startprojekt** aus.

1. Wählen Sie **Mehrere Startprojekte**, dann **Starten** als Aktion für die App und dann **OK** aus, um die Änderungen zu akzeptieren.

1. Drücken Sie **F5**, um die Apps zu starten.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md)

* [Erste Schritte mit IoT Edge](../iot-edge/quickstart-linux.md)