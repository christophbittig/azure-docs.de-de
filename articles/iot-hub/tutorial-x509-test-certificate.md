---
title: 'Tutorial: Testen, ob Geräte mit X.509-Zertifikaten bei Azure IoT Hub authentifiziert werden können | Microsoft-Dokumentation'
description: 'Tutorial: Testen der X.509-Zertifikate für die Authentifizierung bei Azure IoT Hub'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: f3f59cb5346f6a2526ee4e8b0d433b577c9cfa57
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553798"
---
# <a name="tutorial-testing-certificate-authentication"></a>Tutorial: Testen der Zertifikatauthentifizierung

Sie können das folgende C#-Codebeispiel verwenden, um zu testen, ob ein Gerät mit Ihrem Zertifikat bei IoT Hub authentifiziert werden kann. Beachten Sie, dass Sie die folgenden Schritte ausführen müssen, bevor Sie den Testcode ausführen:

* Erstellen eines Zertifikats der Stammzertifizierungsstelle oder der untergeordneten Zertifizierungsstelle
* Hochladen des Zertifikats der Zertifizierungsstelle in Ihre IoT Hub-Instanz
* Nachweisen, dass Sie das Zertifizierungsstellenzertifikat besitzen
* Hinzufügen eines Geräts zu Ihrer IoT Hub-Instanz
* Erstellen eines Gerätezertifikats mit derselben Geräte-ID wie Ihr Gerät

>[!IMPORTANT]
>Beim Authentifizierungsprozess wird überprüft, ob Ihr Gerät dem richtigen IoT Hub-Namen zugeordnet ist. 

## <a name="code-example"></a>Codebeispiel

Im folgenden Codebeispiel wird gezeigt, wie Sie eine C#-Anwendung zum Simulieren des für Ihren IoT-Hub registrierten X.509-Geräts erstellen. Im Beispiel werden Temperatur- und Luftfeuchtigkeitswerte vom simulierten Gerät an Ihren Hub gesendet. In diesem Tutorial wird nur die Geräteanwendung erstellt. Es bleibt den Lesern überlassen, als Übung die IoT Hub-Dienstanwendung zu erstellen, die Antworten auf die vom simulierten Gerät gesendeten Ereignisse sendet.

1. Öffnen Sie Visual Studio, wählen Sie **Neues Projekt erstellen** und dann die Projektvorlage **Konsolen-App (.NET Framework)** aus. Wählen Sie **Weiter** aus.

1. Benennen Sie in **Neues Projekt konfigurieren** das Projekt mit *SimulateX509Device*, und wählen Sie **Erstellen** aus.

   ![Erstellen des X.509-Geräteprojekts in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **SimulateX509Device**, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Wählen Sie in **NuGet-Paket-Manager** die Option **Durchsuchen** aus. Suchen Sie dann nach dem Paket **Microsoft.Azure.Devices.Client**, und wählen Sie es aus. Wählen Sie **Installieren** aus.

   ![Hinzufügen des Geräte-SDK NuGet-Pakets in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    In diesem Schritt wird das NuGet-Paket „Azure IoT-Geräte-SDK“ heruntergeladen und installiert sowie ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

    Geben Sie den folgenden Code ein, und führen Sie ihn aus:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```