---
title: 'Tutorial: Entwickeln von C#-Modulen für Windows mit Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie IoT Edge-Module mit C#-Code erstellen und auf Windows-Geräten bereitstellen, auf denen IoT Edge ausgeführt wird.
services: iot-edge
author: kgremban
ms.author: kgremban
ms.date: 08/03/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, amqp, devx-track-csharp
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 530dbd3abba2c61af8ebab3b5fc625990fc1a391
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130228787"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Entwickeln von C#-IoT Edge-Modulen mit Windows-Containern

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In diesem Artikel wird veranschaulicht, wie Sie Visual Studio zum Entwickeln von C#-Code und anschließenden Bereitstellen auf einem Windows-Gerät verwenden, auf dem Azure IoT Edge ausgeführt wird.

>[!NOTE]
>IoT Edge 1.1 LTS ist der letzte Releasekanal, der Windows-Container unterstützt. Ab Version 1.2 werden Windows-Container nicht mehr unterstützt. Wenn Sie IoT Edge auf Windows-Geräten ausführen möchten, sollten Sie [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) verwenden oder dorthin wechseln.

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, mit dem Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert wird. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Verwenden von Visual Studio zum Erstellen eines IoT Edge-Moduls auf der Grundlage des C# SDK
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Vom Modul werden nur dann Nachrichten in Upstream-Richtung gesendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird veranschaulicht, wie Sie ein Modul in C# mit Visual Studio 2019 entwickeln und dann auf einem Windows-Gerät bereitstellen. Wenn Sie Module unter Verwendung von Linux-Containern entwickeln, lesen Sie stattdessen die Informationen unter [Tutorial: Entwickeln eines C#-IoT Edge-Moduls für Linux-Geräte](tutorial-csharp-module.md).

Informieren Sie sich anhand der folgenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von C#-Modulen mit Windows-Containern:

| C# | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;und&nbsp;2019 |
| -- | :------------------: | :------------------: |
| Windows AMD64-Entwicklung | ![Entwickeln von C#-Modulen für WinAMD64 in Visual Studio Code](./media/tutorial-c-module/green-check.png) | ![Entwickeln von C#-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |
| Windows AMD64-Debugging |   | ![Debuggen von C#-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Richten Sie vor dem Durcharbeiten dieses Tutorials zunächst Ihre Entwicklungsumgebung ein, indem Sie die Anleitung im Tutorial [Entwickeln von IoT Edge-Modulen mit Windows-Containern](tutorial-develop-for-windows.md) befolgen. Nach Abschluss dieses Vorgangs verfügt Ihre Umgebung über die folgenden erforderlichen Komponenten:

* Einen [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Azure im Tarif „Free“ oder „Standard“.
* Ein [Windows-Gerät, auf dem Azure IoT Edge ausgeführt wird](how-to-provision-single-device-windows-symmetric.md)
* Eine Containerregistrierung, z. B. [Azure Container Registry](../container-registry/index.yml)
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) mit der konfigurierten Erweiterung [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/) mit Konfigurationseinstellungen für die Ausführung von Windows-Containern

> [!TIP]
> Wenn Sie Visual Studio 2017 (ab Version 15.7) verwenden, müssen Sie Azure IoT Edge Tools für Visual Studio 2017 aus [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) herunterladen und installieren.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

In diesem Abschnitt erstellen Sie ein IoT Edge-Modulprojekt, indem Sie Visual Studio und die Erweiterung „Azure IoT Edge Tools“ verwenden. Nachdem Sie eine Projektvorlage erstellt haben, fügen Sie ihr neuen Code hinzu, damit das Modul Nachrichten aufgrund der darin gemeldeten Eigenschaften herausfiltern kann.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Mit Azure IoT Edge Tools werden Projektvorlagen für alle unterstützten IoT Edge-Modulsprachen in Visual Studio bereitgestellt. Diese Vorlagen verfügen über alle Dateien und Codeelemente, die Sie zum Bereitstellen eines funktionierenden Moduls zum Testen von IoT Edge benötigen. Sie können Ihnen auch als Ausgangspunkt für die Anpassung mit Ihrer eigenen Geschäftslogik dienen.

1. Öffnen Sie Visual Studio 2019, und wählen Sie **Neues Projekt erstellen** aus.

1. Suchen Sie im Bereich **Neues Projekt erstellen** nach **IoT Edge**, und wählen Sie dann in der Ergebnisliste das Projekt **Azure IoT Edge (Windows amd64)** aus.

   ![Screenshot: IoT Edge-Bereich „Neues Projekt erstellen“](./media/tutorial-csharp-module-windows/new-project.png)

1. Wählen Sie **Weiter** aus.

    Der Bereich **Neues Projekt konfigurieren** wird geöffnet.

   ![Screenshot: Bereich „Neues Projekt konfigurieren“](./media/tutorial-csharp-module-windows/configure-project.png)

1. Geben Sie dem Projekt und der Projektmappe im Bereich **Neues Projekt konfigurieren** aussagekräftigere Namen, z. B. **CSharpTutorialApp**. 

1. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

   Der Bereich **Modul hinzufügen** wird geöffnet.

   ![Screenshot: Bereich „Modul hinzufügen“ zum Konfigurieren Ihres Projekts](./media/tutorial-csharp-module-windows/add-application-and-module.png)

1. Gehen Sie auf der Seite **Neues Projekt konfigurieren** wie folgt vor:

   a. Wählen Sie im Bereich auf der linken Seite die Vorlage **C#-Modul** aus.  
   b. Geben Sie im Feld **Modulname** den Namen **CSharpModule** ein.  
   c. Ersetzen Sie im Feld **Repository-URL** den Text **localhost:5000** durch den Wert für den **Anmeldeserver** aus Ihrer Azure-Containerregistrierung, indem Sie das folgende Format verwenden: `<registry name>.azurecr.io/csharpmodule`

    > [!NOTE]
    > Ein Imagerepository enthält den Namen Ihrer Containerregistrierung und den Namen Ihres Containerimages. Für Ihr Containerimage ist der Wert des Modulprojektnamens bereits vorhanden.  Den Anmeldeserver können Sie im Azure-Portal auf der Übersichtsseite Ihrer Containerregistrierung ermitteln.

1. Wählen Sie **Hinzufügen** aus, um das Projekt zu erstellen.

### <a name="add-your-registry-credentials"></a>Hinzufügen von Registrierungsanmeldeinformationen

Das Bereitstellungsmanifest gibt die Anmeldeinformationen für Ihre Containerregistrierung an die IoT Edge-Runtime weiter. Die Runtime benötigt diese Anmeldeinformationen, um Ihre privaten Images per Pull auf das IoT Edge-Gerät zu übertragen. Verwenden Sie die Anmeldeinformationen aus dem Abschnitt **Zugriffsschlüssel** Ihrer Azure-Containerregistrierung.

1. Öffnen Sie im Projektmappen-Explorer von Visual Studio die Datei *deployment.template.json*.

1. Suchen Sie unter den gewünschten Eigenschaften von „$edgeAgent“ nach der **registryCredentials**-Eigenschaft. Die Registrierungsadresse der Eigenschaft sollte automatisch mit den Informationen eingefügt werden, die Sie beim Erstellen des Projekts angegeben haben. Die Felder für Benutzername und Kennwort sollten Variablennamen enthalten. Beispiel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Öffnen Sie die Umgebungsdatei (ENV) in Ihrer Modullösung. Da die Datei im Projektmappen-Explorer standardmäßig ausgeblendet ist, müssen Sie unter Umständen die Schaltfläche **Alle Dateien anzeigen** auswählen, um sie anzuzeigen. Die ENV-Datei muss die gleichen Variablen für Benutzername und Kennwort wie die Datei *deployment.template.json* enthalten.

1. Fügen Sie die Werte für **Benutzername** und **Kennwort** aus Ihrer Azure-Containerregistrierung hinzu.

1. Speichern Sie Ihre Änderungen an der ENV-Datei.

### <a name="update-the-module-with-custom-code"></a>Aktualisieren des Moduls mit benutzerdefiniertem Code

Der Standardmodulcode empfängt Nachrichten in einer Eingabewarteschlange und leitet sie über eine Ausgabewarteschlange weiter. Fügen Sie zusätzlichen Code hinzu, damit das Modul die Nachrichten im Edge-Bereich verarbeitet, bevor es sie an Ihren IoT-Hub weiterleitet. Aktualisieren Sie das Modul, damit es die Temperaturdaten in jeder Nachricht analysiert und die Nachricht nur dann an den IoT-Hub sendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet.

1. Wählen Sie in Visual Studio **CSharpModule** > **Program.cs** aus.

1. Fügen Sie oben im Namespace **CSharpModule** drei **using**-Anweisungen für Typen hinzu, die im weiteren Verlauf verwendet werden:

    ```csharp
    using System.Collections.Generic;     // For KeyValuePair<>
    using Microsoft.Azure.Devices.Shared; // For TwinCollection
    using Newtonsoft.Json;                // For JsonConvert
    ```

1. Fügen Sie der Klasse **Program** nach der Indikatorvariablen die Variable **temperatureThreshold** hinzu. Die Variable „temperatureThreshold“ legt den Wert fest, den die gemessene Temperatur übersteigen muss, damit die Daten an die IoT Hub-Instanz gesendet werden.

    ```csharp
    static int temperatureThreshold { get; set; } = 25;
    ```

1. Fügen Sie der Klasse **Program** nach den Variablendeklarationen die Klassen **MessageBody**, **Machine** und **Ambient** hinzu. Diese Klassen definieren das erwartete Schema für den Textkörper eingehender Nachrichten.

    ```csharp
    class MessageBody
    {
        public Machine machine {get;set;}
        public Ambient ambient {get; set;}
        public string timeCreated {get; set;}
    }
    class Machine
    {
        public double temperature {get; set;}
        public double pressure {get; set;}
    }
    class Ambient
    {
        public double temperature {get; set;}
        public int humidity {get; set;}
    }
    ```

1. Suchen Sie nach der **Init**-Methode. Diese Methode erstellt und konfiguriert ein **ModuleClient**-Objekt, das es dem Modul ermöglicht, eine Verbindung mit der lokalen Azure IoT Edge-Runtime zum Senden und Empfangen von Nachrichten herzustellen. Der Code registriert außerdem einen Rückruf, um über den Endpunkt **input1** Nachrichten von einem IoT Edge-Hub zu empfangen.

   Ersetzen Sie die gesamte Init-Methode durch den folgenden Code:

   ```csharp
   static async Task Init()
   {
       AmqpTransportSettings amqpSetting = new AmqpTransportSettings(TransportType.Amqp_Tcp_Only);
       ITransportSettings[] settings = { amqpSetting };

       // Open a connection to the Edge runtime.
       ModuleClient ioTHubModuleClient = await ModuleClient.CreateFromEnvironmentAsync(settings);
       await ioTHubModuleClient.OpenAsync();
       Console.WriteLine("IoT Hub module client initialized.");

       // Read the TemperatureThreshold value from the module twin's desired properties.
       var moduleTwin = await ioTHubModuleClient.GetTwinAsync();
       await OnDesiredPropertiesUpdate(moduleTwin.Properties.Desired, ioTHubModuleClient);

       // Attach a callback for updates to the module twin's desired properties.
       await ioTHubModuleClient.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertiesUpdate, null);

       // Register a callback for messages that are received by the module.
       await ioTHubModuleClient.SetInputMessageHandlerAsync("input1", FilterMessages, ioTHubModuleClient);
   }
   ```

   Mit dieser aktualisierten Init-Methode wird auch weiterhin per „ModuleClient“ die Verbindung mit der IoT Edge-Runtime hergestellt, aber sie verfügt über neue Funktionen. Sie liest die gewünschten Eigenschaften des Modulzwillings zum Abrufen des Werts **temperatureThreshold**. Anschließend wird damit ein Rückruf erstellt, der auf alle zukünftigen Aktualisierungen der gewünschten Eigenschaften des Modulzwillings lauscht. Mit diesem Rückruf können Sie den Temperaturschwellenwert im Modulzwilling remote aktualisieren, und die Änderungen werden in das Modul integriert.

   Die aktualisierte Init-Methode ändert darüber hinaus die vorhandene Methode **SetInputMessageHandlerAsync**. Im Beispielcode werden eingehende Nachrichten für *input1* mit der Funktion *PipeMessage* verarbeitet. Der Code soll jedoch so geändert werden, dass stattdessen die Funktion *FilterMessages* verwendet wird, die wir in den nächsten Schritten erstellen.

1. Fügen Sie der Klasse **Program** die neue Methode **onDesiredPropertiesUpdate** hinzu. Diese Methode empfängt Aktualisierungen der gewünschten Eigenschaften aus dem Modulzwilling und aktualisiert die Variable **temperatureThreshold** entsprechend. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in einem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```csharp
    static Task OnDesiredPropertiesUpdate(TwinCollection desiredProperties, object userContext)
    {
        try
        {
            Console.WriteLine("Desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            if (desiredProperties["TemperatureThreshold"]!=null)
                temperatureThreshold = desiredProperties["TemperatureThreshold"];

        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error when receiving desired property: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error when receiving desired property: {0}", ex.Message);
        }
        return Task.CompletedTask;
    }
    ```

1. Entfernen Sie die Beispielmethode **PipeMessage**, und ersetzen Sie sie durch die neue Methode **FilterMessages**. Diese Methode wird aufgerufen, wenn das Modul eine Nachricht vom IoT Edge-Hub empfängt. Sie filtert Nachrichten heraus, mit denen Temperaturen gemeldet werden, die unter dem über den Modulzwilling festgelegten Temperaturschwellenwert liegen. Darüber hinaus fügt die Methode der Nachricht außerdem die **MessageType**-Eigenschaft hinzu, deren Wert auf **Alert** festgelegt ist.

    ```csharp
    static async Task<MessageResponse> FilterMessages(Message message, object userContext)
    {
        var counterValue = Interlocked.Increment(ref counter);
        try
        {
            ModuleClient moduleClient = (ModuleClient)userContext;
            var messageBytes = message.GetBytes();
            var messageString = Encoding.UTF8.GetString(messageBytes);
            Console.WriteLine($"Received message {counterValue}: [{messageString}]");

            // Get the message body.
            var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

            if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
            {
                Console.WriteLine($"Machine temperature {messageBody.machine.temperature} " +
                    $"exceeds threshold {temperatureThreshold}");
                using(var filteredMessage = new Message(messageBytes))
                {
                    foreach (KeyValuePair<string, string> prop in message.Properties)
                    {
                        filteredMessage.Properties.Add(prop.Key, prop.Value);
                    }

                    filteredMessage.Properties.Add("MessageType", "Alert");
                    await moduleClient.SendEventAsync("output1", filteredMessage);
                }
            }

            // Indicate that the message treatment is completed.
            return MessageResponse.Completed;
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
            // Indicate that the message treatment is not completed.
            var moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
            // Indicate that the message treatment is not completed.
            ModuleClient moduleClient = (ModuleClient)userContext;
            return MessageResponse.Abandoned;
        }
    }
    ```

1. Speichern Sie die Datei *Program.cs*.

1. Öffnen Sie die Datei *deployment.template.json* in Ihrer IoT Edge-Projektmappe. Mithilfe dieser Datei wird dem IoT Edge-Agent mitgeteilt, welche Module bereitgestellt werden sollen, und dem IoT Edge-Hub wird mitgeteilt, wie die Nachrichtenweiterleitung dazwischen durchgeführt werden soll. Die Module für die Bereitstellung lauten hier **SimulatedTemperatureSensor** und **CSharpModule**.

1. Fügen Sie dem Bereitstellungsmanifest den Modulzwilling **CSharpModule** hinzu. Fügen Sie am Ende des Abschnitts `modulesContent` nach dem Modulzwilling **$edgeHub** den folgenden JSON-Inhalt ein:

    ```json
       "CSharpModule": {
           "properties.desired":{
               "TemperatureThreshold":25
           }
       }
    ```

    ![Screenshot: Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-csharp-module-windows/module-twin.png)

1. Speichern Sie die Datei *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im obigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CSharpModule** Code hinzugefügt, mit dem Nachrichten herausgefiltert werden, deren gemeldete Computertemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

### <a name="sign-in-to-docker"></a>Anmelden bei Docker

1. Verwenden Sie den folgenden Befehl, um sich auf Ihrem Entwicklungscomputer bei Docker anzumelden. Verwenden Sie den Benutzernamen, das Kennwort und den Anmeldeserver aus Ihrer Azure-Containerregistrierung. Diese Werte finden Sie im Azure-Portal im Abschnitt **Zugriffsschlüssel** Ihrer Registrierung.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Unter Umständen wird eine Sicherheitswarnung angezeigt, in der die Verwendung von `--password-stdin` empfohlen wird. Dies ist zwar eine empfohlene bewährte Methode für Produktionsszenarien, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login-Referenz](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Erstellen und Pushen

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Namen des Projekts, das Sie erstellen möchten. Der Standardname lautet **AzureIotEdgeApp1**. Da Sie ein Windows-Modul erstellen, sollte die Erweiterung **Windows.Amd64** lauten.

1. Wählen Sie **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen) aus.

   Mit dem Befehl zum Erstellen und Übertragen per Push werden drei Vorgänge gestartet:
   * Zuerst wird in der Projektmappe ein neuer Ordner mit dem Namen *config* erstellt, der das vollständige Bereitstellungsmanifest enthält. Es basiert auf den Informationen der Bereitstellungsvorlage und anderer Projektmappendateien. 
   * Anschließend wird `docker build` zum Erstellen des Containerimages ausgeführt, das auf der entsprechenden Dockerfile für Ihre Zielarchitektur basiert. 
   * Abschließend wird dann `docker push` ausgeführt, um das Imagerepository per Push in Ihre Containerregistrierung zu übertragen.

   Dieser Vorgang kann beim ersten Mal ggf. einige Minuten dauern, aber die nächste Ausführung der Befehle geht dann schon schneller.

## <a name="deploy-modules-to-the-device"></a>Bereitstellen von Modulen auf dem Gerät

Verwenden Sie Visual Studio Cloud-Explorer und die Erweiterung „Azure IoT Edge Tools“ zum Bereitstellen des Modulprojekts auf Ihrem IoT Edge-Gerät. Sie haben schon ein Bereitstellungsmanifest für Ihr Szenario vorbereitet: die Datei *deployment.windows-amd64.json* im Ordner *config*. Nun müssen Sie nur noch ein Gerät auswählen, um die Bereitstellung zu empfangen.

Sorgen Sie dafür, dass Ihr IoT Edge-Gerät ordnungsgemäß ausgeführt wird.

1. Erweitern Sie im Cloud-Explorer von Visual Studio die Ressourcen, um Ihre Liste mit den IoT-Geräten anzuzeigen.

1. Klicken Sie mit der rechten Maustaste auf den Namen des IoT Edge-Geräts, das die Bereitstellung erhalten soll.

1. Wählen Sie **Bereitstellung erstellen** aus.

1. Wählen Sie im Datei-Explorer von Visual Studio im Ordner *config* Ihrer Projektmappe die Datei *deployment.windows-amd64.json* aus.

1. Aktualisieren Sie den Cloud-Explorer, um die bereitgestellten Module anzuzeigen, die unter Ihrem Gerät aufgeführt sind.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

Nachdem Sie das Bereitstellungsmanifest auf Ihr IoT Edge-Gerät angewendet haben, erfasst die IoT Edge-Runtime auf dem Gerät die neuen Bereitstellungsinformationen und verwendet sie bei der Ausführung. Alle auf dem Gerät ausgeführten Module, die nicht im Bereitstellungsmanifest enthalten sind, werden beendet. Alle Module, die auf dem Gerät fehlen, werden gestartet.

Mit der Erweiterung „IoT Edge Tools“ können Sie die auf Ihrem IoT-Hub eingehenden Nachrichten anzeigen.

1. Wählen Sie in Visual Studio Cloud-Explorer den Namen Ihres IoT Edge-Geräts aus.

1. Wählen Sie in der Liste **Aktionen** die Option **Start Monitoring Built-in Event Endpoint** (Überwachung des integrierten Ereignisendpunkts starten) aus.

1. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Unter Umständen dauert es eine Weile, bis die Nachrichten eingehen, weil das IoT Edge-Gerät erst seine neue Bereitstellung empfangen und alle Module starten muss. Die Änderungen am CSharpModule-Code müssen warten, bis die Computertemperatur 25 Grad erreicht hat. Erst dann können die Nachrichten gesendet werden. Außerdem wird mit dem Code allen Nachrichten, für die dieser Temperaturschwellenwert erreicht wird, der Nachrichtentyp **Alert** (Warnung) hinzugefügt.

   ![Screenshot: Anzeige von Nachrichten, die auf dem IoT-Hub eintreffen, im Ausgabefenster](./media/tutorial-csharp-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „CSharpModule“ verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Öffnen Sie in Visual Studio die Datei *deployment.windows-amd64.json*. 

   Öffnen Sie *nicht* die Datei *deployment.template*. Falls das Bereitstellungsmanifest nicht in der Datei *config* im Projektmappen-Explorer angezeigt wird, wählen Sie in der Symbolleiste des Projektmappen-Explorers das Symbol **Alle Dateien anzeigen** aus.

1. Suchen Sie nach dem Zwilling „CSharpModule“, und ändern Sie den Wert des Parameters **temperatureThreshold** in eine neue Temperatur, die fünf bis zehn Grad über der zuletzt gemeldeten Temperatur liegt.

1. Speichern Sie die Datei *deployment.windows-amd64.json*.

1. Befolgen Sie erneut die Bereitstellungsanleitung, um das aktualisierte Bereitstellungsmanifest auf Ihr Gerät anzuwenden.

1. Überwachen Sie die eingehenden D2C-Nachrichten (Device-to-Cloud, Gerät-zu-Cloud). Die Nachrichten sollten so lange gestoppt werden, bis der neue Temperaturschwellenwert erreicht wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie mit dem nächsten empfohlenen Artikel fortfahren möchten, können Sie die in diesem Tutorial erstellten Ressourcen und Konfigurationen beibehalten und wiederverwenden. Sie können auch dasselbe IoT Edge-Gerät als Testgerät weiter nutzen.

Andernfalls können Sie die hier verwendeten lokalen Konfigurationen und die Azure-Ressourcen löschen, um das Anfallen von Kosten zu vermeiden.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein IoT Edge-Modul mit Code zum Filtern von Rohdaten erstellt, die von Ihrem IoT Edge-Gerät generiert werden.

Arbeiten Sie die nächsten Tutorials durch, um zu erfahren, wie Ihnen Azure IoT Edge bei der Bereitstellung von Azure-Clouddiensten helfen kann, um Daten auf Edge-Ebene zu verarbeiten und zu analysieren.

> [!div class="nextstepaction"]
> [Azure Functions](tutorial-deploy-function.md)
> [Azure Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Azure Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision-Dienst](tutorial-deploy-custom-vision.md)
