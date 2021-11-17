---
title: Modulidentität und Modulzwilling von Azure IoT Hub (Portal und .NET)
description: Hier erfahren Sie, wie Sie mit dem Portal und .NET eine Modulidentität erstellen und den Modulzwilling aktualisieren.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 993a59f1c47bd0920c120a19252367489c768807
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555071"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub unter Verwendung des Portals und eines .NET-Geräts

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modulidentitäten und Modulzwillinge](iot-hub-devguide-module-twins.md) ähneln den Geräteidentitäten und Gerätezwillingen von Azure IoT Hub, ermöglichen jedoch eine feinere Granularität. Azure IoT Hub-Geräteidentitäten und -Gerätezwillinge ermöglichen der Back-End-Anwendung die Konfiguration eines Geräts und geben Aufschluss über den Gerätezustand. Modulidentitäten und Modulzwillinge bieten diese Funktionalität für einzelne Komponenten eines Geräts. Auf Geräten mit mehreren Komponenten (etwa auf betriebssystembasierten Geräten oder Firmwaregeräten) ermöglichen Modulidentitäten und Modulzwillinge isolierte Konfigurationen und Zustände für die einzelnen Komponenten.
>

In diesem Tutorial lernen Sie Folgendes:

* Erstellen einer Modulidentität im Portal

* Aktualisieren des Modulzwillings auf Ihrem Gerät mithilfe eines .NET-Geräte-SDKs

> [!NOTE]
> Informationen zu den verschiedenen Azure IoT SDKs, mit denen Sie sowohl Anwendungen für Geräte als auch das zugehörige Lösungs-Back-End erstellen können, finden Sie unter [Azure IoT SDKs](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Voraussetzungen

* Visual Studio.

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

## <a name="create-a-hub"></a>Erstellen eines Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Registrieren eines neuen Geräts beim Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Erstellen einer Modulidentität im Portal

Innerhalb einer Geräteidentität können Sie bis zu 20 Modulidentitäten erstellen. Führen Sie die folgenden Schritte aus, um eine Identität hinzufügen:

1. Wählen Sie für das Gerät, das Sie im vorherigen Abschnitt erstellt haben, **Modulidentität hinzufügen** aus, um Ihre erste Modulidentität zu erstellen.

1. Geben Sie den Namen *myfirstmodule* ein. Speichern Sie Ihre Modulidentität.

    ![Modulidentität hinzufügen](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Die neue Modulidentität wird unten auf dem Bildschirm angezeigt. Wählen Sie sie aus, um Details zur Modulidentität anzuzeigen.

    ![Details zur Modulidentität anzeigen](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Speichern Sie **Verbindungszeichenfolge – Primärschlüssel**. Sie verwenden sie im nächsten Abschnitt, um Ihr Modul auf dem Gerät einzurichten.

## <a name="update-the-module-twin-using-net-device-sdk"></a>Aktualisieren des Modulzwillings mithilfe des .NET-Geräte-SDKs

Sie haben die Modulidentität in Ihrer IoT Hub-Instanz erstellt. Testen wir nun die Kommunikation zwischen Cloud und simuliertem Gerät. Sobald eine Modulidentität erstellt wurde, wird implizit ein Gerätezwilling in IoT Hub erstellt. In diesem Abschnitt erstellen Sie eine .NET Konsolen-App auf Ihrem simulierte Gerät, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert.

### <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

Führen Sie die folgenden Schritte aus, um eine App zu erstellen, die die vom Modulzwilling gemeldeten Eigenschaften aktualisiert:

1. Wählen Sie in Visual Studio **Neues Projekt erstellen**, anschließend **Konsolen-App (.NET Framework)** und dann **Weiter** aus.

1. Geben Sie in **Neues Projekt konfigurieren** die Zeichenfolge *UpdateModuleTwinReportedProperties* als **Projektname** ein. Klicken Sie auf **Erstellen** , um fortzufahren.

    ![Konfigurieren Ihres Visual Studio-Projekts](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>Installieren des neuesten Azure IoT Hub-.NET-Geräte-SDKs

Die Modulidentität und der Modulzwilling befinden sich in der Public Preview. Sie sind nur in der Vorabversion der IoT Hub-Geräte-SDKs verfügbar. Führen Sie die folgenden Schritte aus, um sie zu installieren:

1. Navigieren Sie in Visual Studio zu **Tools** > **NuGet-Paket-Manager** > **NuGet-Pakete für Projektmappe verwalten**.

1. Wählen Sie **Durchsuchen** und dann **Vorabversion einbeziehen** aus. Suchen Sie nach *Microsoft.Azure.Devices.Client*. Wählen Sie die neueste Version aus, und installieren Sie sie.

    ![Installieren des Azure IoT Hub-.NET-Dienst-SDKs (Vorschau)](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Sie haben nun Zugriff auf alle Modulfeatures.

### <a name="get-your-module-connection-string"></a>Abrufen der Modulverbindungszeichenfolge

Sie benötigen die Verbindungszeichenfolge des Moduls für Ihre Konsolen-App. Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem IoT-Hub, und wählen Sie **IoT-Geräte** aus. Öffnen Sie **myFirstDevice**. Dann sehen Sie, dass **myFirstModule** erfolgreich erstellt wurde.

1. Wählen Sie unter **Modulidentitäten** den Namen **myFirstModule** aus. Kopieren Sie in **Details zur Modulidentität** das Detail **Verbindungszeichenfolge (Primärschlüssel)** .

    ![Moduldetails im Azure-Portal](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Erstellen der Konsolen-App „UpdateModuleTwinReportedProperties“

Führen Sie die folgenden Schritte aus, um Ihre App zu erstellen:

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Verbindungszeichenfolge des Moduls.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Fügen Sie die folgende Methode **OnDesiredPropertyChanged** der Klasse **Program** hinzu:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. Ersetzen Sie schließlich die **Main**-Methode durch den folgenden Code:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  Sie können diese App mit **F5** erstellen und ausführen.

In diesem Codebeispiel wird gezeigt, wie Sie den Modulzwilling abrufen und die gemeldeten Eigenschaften mit dem AMQP-Protokoll aktualisieren. In der öffentlichen Vorschau wird nur AMQP für Modulzwillingsvorgänge unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den weiteren ersten Schritten mit IoT Hub und zum Kennenlernen anderer IoT-Szenarien finden Sie in den folgenden Artikeln:

* [Erste Schritte mit der Modulidentität und dem Modulzwilling von IoT Hub unter Verwendung einer .NET-Sicherung und eines .NET-Geräts](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Erste Schritte mit IoT Edge](../iot-edge/quickstart-linux.md)