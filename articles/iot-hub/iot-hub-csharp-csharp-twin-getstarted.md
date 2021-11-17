---
title: Erste Schritte mit Azure IoT Hub-Gerätezwillingen (.NET/.NET) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure IoT Hub-Gerätezwillingen Tags hinzufügen und eine IoT Hub-Abfrage verwenden. Sie verwenden das Azure IoT-Geräte-SDK für .NET, um eine simulierte Geräte-App zu implementieren, und das Azure IoT-Dienst-SDK für .NET, um eine Dienst-App zu implementieren, die die Tags hinzufügt und die IoT Hub-Abfrage ausführt.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: lizross
ms.custom: mqtt, devx-track-csharp
ms.openlocfilehash: ea2b7048d7118afb1f83e524d75a172a478f779e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132554862"
---
# <a name="get-started-with-device-twins-net"></a>Erste Schritte mit Gerätezwillingen (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

In diesem Tutorial erstellen Sie zwei .NET-Konsolen-Apps:

* **AddTagsAndQuery:** Diese Back-End-App fügt Tags hinzu und fragt Gerätezwillinge ab.

* **ReportConnectivity:** Diese Geräte-App simuliert ein Gerät, das eine Verbindung mit Ihrem IoT-Hub mit der zuvor erstellten Geräteidentität herstellt und seine Verbindungsbedingung meldet.

> [!NOTE]
> Im Artikel [Azure IoT SDKs](iot-hub-devguide-sdks.md) finden Sie Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Geräte- als auch Back-End-Apps erstellen können.
>

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Stellen Sie sicher, dass der Port 8883 in Ihrer Firewall geöffnet ist. Das Beispielgerät in diesem Artikel verwendet das MQTT-Protokoll, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrieren eines neuen Geräts beim IoT-Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Abrufen der IoT-Hub-Verbindungszeichenfolge

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Erstellen der Dienst-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App in C#, mit der dem Gerätezwilling, der **myDeviceId** zugeordnet ist, Standortmetadaten hinzugefügt werden. Anschließend werden die in dem IoT Hub, der die Geräte in den USA auswählt, gespeicherten Gerätezwillinge abgefragt und dann diejenigen, die eine Mobilfunkverbindung gemeldet haben.

1. Wählen Sie in Visual Studio **Neues Projekt erstellen** aus. Wählen Sie in **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie dem Projekt unter **Neues Projekt konfigurieren** den Namen **AddTagsAndQuery**.

    ![Konfigurieren des Projekts „AddTagsAndQuery“](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AddTagsAndQuery**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

1. Wählen Sie **Durchsuchen** aus, suchen Sie nach dem Paket **Microsoft.Azure.Devices**, und wählen Sie es aus. Wählen Sie **Installieren** aus.

    ![Fenster „NuGet-Paket-Manager“](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   In diesem Schritt wird das NuGet-Paket [Azure IoT-Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie `{iot hub connection string}` durch die IoT-Hub-Verbindungszeichenfolge, die Sie unter [Abrufen der IoT-Hub-Verbindungszeichenfolge](#get-the-iot-hub-connection-string) kopiert haben.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    Die **Registry**-Klasse macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Diensts erforderlich sind. Mit dem vorherigen Code werden zunächst das **registryManager**-Objekt initialisiert, dann der Gerätezwilling für **myDeviceId** abgerufen und schließlich die zugehörigen Tags mit den gewünschten Standortinformationen aktualisiert.

    Nach der Aktualisierung werden zwei Abfragen ausgeführt: Mit der ersten werden nur die Gerätezwillinge von Geräten in der Anlage **Redmond43** ausgewählt. Mit der zweiten wird die Abfrage so angepasst, dass nur die Geräte ausgewählt werden, die über ein Mobilfunknetz verbunden sind.

    Mit diesem Code wird beim Erstellen des **query**-Objekts eine maximale Anzahl von zurückgegebenen Dokumenten angegeben. Das **query**-Objekt enthält die boolesche Eigenschaft **HasMoreResults**, mit der Sie die **GetNextAsTwinAsync**-Methoden mehrmals aufrufen können, um alle Ergebnisse abzurufen. Die **GetNextAsJson**-Methode ist für Ergebnisse verfügbar, die keine Gerätezwillinge sind, z.B. Ergebnisse von Aggregationsabfragen.

1. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Führen Sie diese Anwendung aus, indem Sie mit der rechten Maustaste auf das Projekt **AddTagsAndQuery** klicken und **Debuggen** und anschließend **Neue Instanz starten** auswählen. In den Ergebnissen für die Abfrage, mit der alle Geräte in der Anlage **Redmond43** abgefragt werden, sollte ein Gerät angezeigt werden und keines für die Abfrage, mit der die Ergebnisse auf die über ein Mobilfunknetz verbundenen Geräte beschränkt werden.

    ![Abfrageergebnisse im Fenster](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Im nächsten Abschnitt erstellen Sie eine Geräte-App, mit der die Verbindungsinformationen gemeldet und das Ergebnis der im vorherigen Abschnitt ausgeführten Abfrage geändert wird.

## <a name="create-the-device-app"></a>Erstellen der Geräte-App

In diesem Abschnitt erstellen Sie eine .NET-Konsolen-App, die als **myDeviceId** eine Verbindung mit dem Hub herstellt und dann die gemeldeten Eigenschaften so aktualisiert, dass sie die Informationen über die Verbindung mit einem Mobilfunknetz enthalten.

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**. Wählen Sie unter **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie dem Projekt unter **Neues Projekt konfigurieren** den Namen **ReportConnectivity**. Wählen Sie für **Lösung** die Option **Zur Projektmappe hinzufügen** aus, und wählen Sie dann **Erstellen** aus.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ReportConnectivity**, und wählen Sie dann **NuGet-Pakete verwalten** aus.

1. Wählen Sie **Durchsuchen** aus, suchen Sie nach dem Paket **Microsoft.Azure.Devices.Client**, und wählen Sie es aus. Wählen Sie **Installieren** aus.

   In diesem Schritt wird das NuGet-Paket [Azure IoT-Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) heruntergeladen und installiert sowie ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie `{device connection string}` durch die Geräteverbindungszeichenfolge, die Sie unter [Registrieren eines neuen Geräts beim IoT-Hub](#register-a-new-device-in-the-iot-hub) notiert haben.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Das **Client**-Objekt macht alle Methoden verfügbar, die für die Interaktion mit Gerätezwillingen des Geräts erforderlich sind. Der oben gezeigte Code initialisiert das **Client**-Objekt und ruft dann den Gerätezwilling für **myDeviceId** ab.

1. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Der Code oben aktualisiert die für **myDeviceId** gemeldete Eigenschaft mit den Konnektivitätsinformationen.

1. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie **Startprojekte festlegen** aus.

1. Wählen Sie unter **Allgemeine Eigenschaften** > **Startprojekt** die Option **Mehrere Startprojekte** aus. Wählen Sie für **ReportConnectivity** die Option **Starten** als **Aktion** aus. Klicken Sie zum Speichern der Änderungen auf **OK** .  

1. Sie führen diese App aus, indem Sie mit der rechten Maustaste auf das Projekt **ReportConnectivity** klicken und **Debuggen** und dann **Neue Instanz starten** auswählen. Sie sollten sehen, wie die Zwillingsinformationen für die App abgerufen und die Konnektivität anschließend als **_gemeldete Eigenschaft_** gesendet wird.

    ![Ausführen der Geräte-App zum Melden der Konnektivität](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Nachdem das Gerät die Verbindungsinformationen gemeldet hat, sollten sie in beiden Abfragen angezeigt werden.

1. Klicken Sie mit der rechten Maustaste auf das Projekt **AddTagsAndQuery**, und wählen Sie **Debuggen** > **Neue Instanz starten** aus, um die Abfragen erneut auszuführen. Nun sollte **myDeviceId** in beiden Abfrageergebnissen angezeigt werden.

    ![Gerätekonnektivität erfolgreich gemeldet](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie im Azure-Portal einen neuen IoT-Hub konfiguriert und anschließend in der Identitätsregistrierung des IoT-Hubs eine Geräteidentität erstellt. Sie haben Gerätemetadaten aus einer Back-End-App als Tags hinzugefügt und eine simulierte Geräte-App zum Melden von Geräteverbindungsinformationen im Gerätezwilling geschrieben. Sie haben zudem erfahren, wie diese Informationen mithilfe der SQL-ähnlichen IoT Hub-Abfragesprache abgefragt werden können.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* Informationen zum Senden von Telemetriedaten von Geräten finden Sie im Tutorial [Senden von Telemetriedaten von einem Gerät an einen IoT-Hub](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp).

* Informationen zum Konfigurieren von Geräten mit den gewünschten Eigenschaften des Gerätezwillings finden Sie im Tutorial [Verwenden von gewünschten Eigenschaften zum Konfigurieren von Geräten](tutorial-device-twins.md).

* Informationen zum interaktiven Steuern von Geräten (z. B. Einschalten eines Lüfters über eine benutzergesteuerte App) finden Sie im Quickstart [Verwenden von direkten Methoden](./quickstart-control-device.md?pivots=programming-language-csharp).