---
title: 'Tutorial: Entwickeln von C-Modulen für Windows mit Azure IoT Edge'
description: In diesem Tutorial wird gezeigt, wie Sie IoT Edge-Module mit C-Code erstellen und auf Windows-Geräten bereitstellen, auf denen IoT Edge ausgeführt wird.
services: iot-edge
author: kgremban
ms.author: kgremban
ms.date: 05/28/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 1c01488f23b67d3aad54986fa73c4567a6f28365
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749561"
---
# <a name="tutorial-develop-c-iot-edge-modules-using-windows-containers"></a>Tutorial: Entwickeln von C-IoT Edge-Modulen mit Windows-Containern

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In diesem Artikel wird veranschaulicht, wie Sie Visual Studio zum Entwickeln von C-Code und Bereitstellen auf einem Windows-Gerät verwenden, auf dem Azure IoT Edge ausgeführt wird.

>[!NOTE]
>IoT Edge 1.1 LTS ist der letzte Releasekanal, der Windows-Container unterstützt. Ab Version 1.2 werden Windows-Container nicht mehr unterstützt. Wenn Sie IoT Edge auf Windows-Geräten ausführen möchten, sollten Sie [IoT Edge für Linux unter Windows](iot-edge-for-linux-on-windows.md) verwenden oder dorthin wechseln.

Mithilfe von Azure IoT Edge-Modulen können Sie Code bereitstellen, mit dem Ihre Geschäftslogik direkt auf Ihren IoT Edge-Geräten implementiert wird. In diesem Tutorial wird beschrieben, wie Sie ein IoT Edge-Modul, mit dem Sensordaten gefiltert werden, erstellen und bereitstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Verwenden von Visual Studio zum Erstellen eines IoT Edge-Moduls auf der Grundlage des C SDK
> * Erstellen und Veröffentlichen eines Docker-Images in Ihrer Registrierung unter Verwendung von Visual Studio und Docker
> * Bereitstellen des Moduls auf Ihrem IoT Edge-Gerät
> * Anzeigen generierter Daten

Das IoT Edge-Modul, das Sie in diesem Tutorial erstellen, filtert die von Ihrem Gerät generierten Temperaturdaten. Vom Modul werden nur dann Nachrichten in Upstream-Richtung gesendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet. Diese Art der Analyse auf Edge-Ebene trägt zur Verringerung der Datenmenge bei, die an die Cloud übermittelt und dort gespeichert wird.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird veranschaulicht, wie Sie ein Modul in C mit Visual Studio 2019 entwickeln und dann auf einem Windows-Gerät bereitstellen. Wenn Sie Module unter Verwendung von Linux-Containern entwickeln, lesen Sie stattdessen die Informationen unter [Tutorial: Entwickeln eines C-IoT Edge-Moduls für Linux-Geräte](tutorial-csharp-module.md).

Informieren Sie sich anhand der folgenden Tabelle über Ihre Optionen zum Entwickeln und Bereitstellen von C-Modulen mit Windows-Containern:

| C | Visual&nbsp;Studio&nbsp;Code | Visual Studio 2017&nbsp;uand&nbsp;2019 |
| -- | ------------------ | :------------------: |
| Windows AMD64 |  | ![Entwickeln von C-Modulen für WinAMD64 in Visual Studio](./media/tutorial-c-module/green-check.png) |

Richten Sie vor dem Durcharbeiten dieses Tutorials zunächst Ihre Entwicklungsumgebung ein, indem Sie die Anleitung im Tutorial [Entwickeln von IoT Edge-Modulen mit Windows-Containern](tutorial-develop-for-windows.md) befolgen. Nach Abschluss dieses Vorgangs verfügt Ihre Umgebung über die folgenden erforderlichen Komponenten:

* Einen [IoT-Hub](../iot-hub/iot-hub-create-through-portal.md) in Azure im Tarif „Free“ oder „Standard“.
* Ein [Windows-Gerät, auf dem Azure IoT Edge ausgeführt wird](how-to-install-iot-edge-windows-on-windows.md)
* Eine Containerregistrierung, z. B. [Azure Container Registry](../container-registry/index.yml)
* [Visual Studio 2019](/visualstudio/install/install-visual-studio) mit der konfigurierten Erweiterung [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)
* [Docker Desktop](https://docs.docker.com/docker-for-windows/install/), zur Ausführung von Windows-Containern konfiguriert.

Installieren Sie das Azure IoT C SDK für Windows x64 über vcpkg, indem Sie die folgenden Befehle ausführen:

   ```powershell
   git clone https://github.com/Microsoft/vcpkg
   cd vcpkg
   .\bootstrap-vcpkg.bat
   .\vcpkg install azure-iot-sdk-c:x64-windows
   .\vcpkg --triplet x64-windows integrate install
   ```

> [!TIP]
> Wenn Sie Visual Studio 2017 (ab Version 15.7) verwenden, müssen Sie die Azure IoT Edge Tools für Visual Studio 2017 aus [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) herunterladen und installieren.

## <a name="create-a-module-project"></a>Erstellen eines Modulprojekts

In diesem Abschnitt Erstellen Sie ein auf dem C SDK basierendes IoT Edge-Modul unter Verwendung von Visual Studio und der Erweiterung „Azure IoT Edge Tools“. Nachdem Sie eine Projektvorlage erstellt haben, fügen Sie ihr neuen Code hinzu, damit das Modul Nachrichten aufgrund der darin gemeldeten Eigenschaften herausfiltern kann.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Erstellen Sie eine C-Lösungsvorlage, die Sie mit eigenem Code anpassen können.

1. Öffnen Sie Visual Studio 2019, und wählen Sie **Neues Projekt erstellen** aus.

1. Suchen Sie im Bereich **Neues Projekt erstellen** nach **IoT Edge**, und wählen Sie dann in der Ergebnisliste das Projekt **Azure IoT Edge (Windows amd64)** aus.

   ![Screenshot: IoT Edge-Bereich „Neues Projekt erstellen“](./media/tutorial-c-module-windows/new-project.png)

1. Wählen Sie **Weiter** aus.

    Der Bereich **Neues Projekt konfigurieren** wird geöffnet.

   ![Screenshot: Bereich „Neues Projekt konfigurieren“](./media/tutorial-c-module-windows/configure-project.png)

1. Geben Sie dem Projekt und der Projektmappe im Bereich **Neues Projekt konfigurieren** einen aussagekräftigeren Namen, z. B. **CTutorialApp**. 

1. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

   Der Bereich **Modul hinzufügen** wird geöffnet.

   ![Screenshot: Bereich „Modul hinzufügen“ zum Konfigurieren Ihres Projekts](./media/tutorial-c-module-windows/add-application-and-module.png)

1. Gehen Sie auf der Seite **Neues Projekt konfigurieren** wie folgt vor:

   a. Wählen Sie im Bereich auf der linken Seite die Vorlage **C-Modul** aus.  
   b. Geben Sie im Feld **Modulname** den Namen **CModule** ein.  
   c. Ersetzen Sie im Feld **Repository-URL** den Text **localhost:5000** durch den Wert für den **Anmeldeserver** aus Ihrer Azure-Containerregistrierung, indem Sie das folgende Format verwenden: `<registry name>.azurecr.io/cmodule`

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

Der Standardmodulcode empfängt Nachrichten in einer Eingabewarteschlange und leitet sie über eine Ausgabewarteschlange weiter. Fügen Sie einigen zusätzlichen Code hinzu, damit das Modul die Nachrichten im Edge-Bereich verarbeitet, bevor es sie an Ihren IoT-Hub weiterleitet. Aktualisieren Sie das Modul, sodass es die Temperaturdaten in jeder Nachricht analysiert und die Nachricht nur dann an den IoT-Hub sendet, wenn die Temperatur einen bestimmten Schwellenwert überschreitet.

1. Die Sensordaten in diesem Szenario liegen im JSON-Format vor. Importieren Sie zum Filtern von Nachrichten im JSON-Format eine JSON-Bibliothek für C. In diesem Tutorial wird Parson verwendet.

   a. Laden Sie das [Parson-Repository von GitHub](https://github.com/kgabis/parson) herunter.  
   b. Kopieren Sie die Dateien *parson.c* und *parson.h* in das Projekt „CModule“.  
   c. Öffnen Sie in Visual Studio die Datei *CMakeLists.txt* aus dem CModule-Projektordner.  
   d. Importieren Sie am Anfang der Datei die Parson-Dateien als Bibliothek namens **my_parson**.

      ```txt
      add_library(my_parson
          parson.c
          parson.h
      )
      ```  
   e. Fügen Sie in der Datei *CMakeLists.txt* im Abschnitt „target_link_libraries“ der Liste mit den Bibliotheken den Eintrag `my_parson` hinzu.  
   f. Speichern Sie die Datei *CMakeLists.txt*.  
   g. Wählen Sie **CModule** > **main.c** aus. Fügen Sie am Ende der Liste mit include-Anweisungen eine neue Anweisung hinzu, um `parson.h` zur JSON-Unterstützung einzuschließen:

      ```c
      #include "parson.h"
      ```

1. Fügen Sie in der Datei *main.c* eine globale Variable namens `temperatureThreshold` neben der Variablen `messagesReceivedByInput1Queue` hinzu. Diese Variable legt den Wert fest, den die gemessene Temperatur übersteigen muss, damit die Daten an Ihre IoT Hub-Instanz gesendet werden.

    ```c
    static double temperatureThreshold = 25;
    ```

1. Suchen Sie die Funktion `CreateMessageInstance` in *main.c*. Ersetzen Sie die innere Anweisung *if-else* durch den folgenden Code, der ein paar Zeilen mit Funktionen hinzufügt:

   ```c
   if ((messageInstance->messageHandle = IoTHubMessage_Clone(message)) == NULL)
   {
       free(messageInstance);
       messageInstance = NULL;
   }
   else
   {
       messageInstance->messageTrackingId = messagesReceivedByInput1Queue;
       MAP_HANDLE propMap = IoTHubMessage_Properties(messageInstance->messageHandle);
       if (Map_AddOrUpdate(propMap, "MessageType", "Alert") != MAP_OK)
       {
          printf("ERROR: Map_AddOrUpdate Failed!\r\n");
       }
   }
   ```

   Die neuen Codezeilen in der Anweisung vom Typ *else* fügen der Nachricht eine neue Eigenschaft hinzu, wodurch sie als Warnung gekennzeichnet wird. Dieser Code kennzeichnet alle Nachrichten als Warnungen, weil Sie eine Funktionalität hinzufügen werden, die Nachrichten nur dann an IoT Hub sendet, wenn hohe Temperaturen gemeldet werden.

1. Suchen Sie die Funktion `InputQueue1Callback`, und ersetzen Sie die gesamte Funktion durch den folgenden Code. Diese Funktion implementiert den Nachrichtenfilter. Wenn eine Nachricht empfangen wird, überprüft er, ob die gemeldete Temperatur den Schwellenwert überschreitet. Wenn die Temperatur den Schwellenwert überschreitet, leitet die Funktion die Nachricht über die Ausgabewarteschlange weiter. Wenn der Schwellenwert nicht überschritten wird, ignoriert die Funktion die Nachricht.

    ```c
    static unsigned char *bytearray_to_str(const unsigned char *buffer, size_t len)
    {
        unsigned char *ret = (unsigned char *)malloc(len + 1);
        memcpy(ret, buffer, len);
        ret[len] = '\0';
        return ret;
    }

    static IOTHUBMESSAGE_DISPOSITION_RESULT InputQueue1Callback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
        IOTHUBMESSAGE_DISPOSITION_RESULT result;
        IOTHUB_CLIENT_RESULT clientResult;
        IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle = (IOTHUB_MODULE_CLIENT_LL_HANDLE)userContextCallback;

        unsigned const char* messageBody;
        size_t contentSize;

        if (IoTHubMessage_GetByteArray(message, &messageBody, &contentSize) == IOTHUB_MESSAGE_OK)
        {
            messageBody = bytearray_to_str(messageBody, contentSize);
        } else
        {
            messageBody = "<null>";
        }

        printf("Received Message [%zu]\r\n Data: [%s]\r\n",
                messagesReceivedByInput1Queue, messageBody);

        // Check whether the message reports temperatures that exceed the threshold
        JSON_Value *root_value = json_parse_string(messageBody);
        JSON_Object *root_object = json_value_get_object(root_value);
        double temperature;

        // If temperature exceeds the threshold, send to output1
        if (json_object_dotget_value(root_object, "machine.temperature") != NULL && (temperature = json_object_dotget_number(root_object, "machine.temperature")) > temperatureThreshold)
        {
            printf("Machine temperature %f exceeds threshold %f\r\n", temperature, temperatureThreshold);
            // This message should be sent to next stop in the pipeline, namely "output1".  What happens at "outpu1" is determined
            // by the configuration of the Edge routing table setup.
            MESSAGE_INSTANCE *messageInstance = CreateMessageInstance(message);
            if (NULL == messageInstance)
            {
                result = IOTHUBMESSAGE_ABANDONED;
            }
            else
            {
                printf("Sending message (%zu) to the next stage in pipeline\n", messagesReceivedByInput1Queue);

                clientResult = IoTHubModuleClient_LL_SendEventToOutputAsync(iotHubModuleClientHandle, messageInstance->messageHandle, "output1", SendConfirmationCallback, (void *)messageInstance);
                if (clientResult != IOTHUB_CLIENT_OK)
                {
                    IoTHubMessage_Destroy(messageInstance->messageHandle);
                    free(messageInstance);
                    printf("IoTHubModuleClient_LL_SendEventToOutputAsync failed on sending msg#=%zu, err=%d\n", messagesReceivedByInput1Queue, clientResult);
                    result = IOTHUBMESSAGE_ABANDONED;
                }
                else
                {
                    result = IOTHUBMESSAGE_ACCEPTED;
                }
            }
        }
        // If message does not exceed the threshold, do not forward
        else
        {
            printf("Not sending message (%zu) to the next stage in pipeline.\r\n", messagesReceivedByInput1Queue);
            result = IOTHUBMESSAGE_ACCEPTED;
        }

        messagesReceivedByInput1Queue++;
        return result;
    }
    ```

1. Fügen Sie die Funktion `moduleTwinCallback` hinzu. Diese Methode empfängt Aktualisierungen der gewünschten Eigenschaften aus dem Modulzwilling und aktualisiert die Variable **temperatureThreshold** entsprechend. Alle Module haben ihren eigenen Modulzwilling, über den Sie den in dem Modul ausgeführten Code direkt in der Cloud konfigurieren können.

    ```c
    static void moduleTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
    {
        printf("\r\nTwin callback called with (state=%s, size=%zu):\r\n%s\r\n",
            MU_ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
        JSON_Value *root_value = json_parse_string(payLoad);
        JSON_Object *root_object = json_value_get_object(root_value);
        if (json_object_dotget_value(root_object, "desired.TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_dotget_number(root_object, "desired.TemperatureThreshold");
        }
        if (json_object_get_value(root_object, "TemperatureThreshold") != NULL) {
            temperatureThreshold = json_object_get_number(root_object, "TemperatureThreshold");
        }
    }
    ```

1. Suchen Sie nach der `SetupCallbacksForModule`-Funktion. Ersetzen Sie die Funktion durch den folgenden Code. Er fügt eine *else-if*-Anweisung hinzu, mit der überprüft wird, ob der Modulzwilling aktualisiert wurde.

   ```c
   static int SetupCallbacksForModule(IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle)
   {
       int ret;

       if (IoTHubModuleClient_LL_SetInputMessageCallback(iotHubModuleClientHandle, "input1", InputQueue1Callback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetInputMessageCallback(\"input1\")..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else if (IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, moduleTwinCallback, (void*)iotHubModuleClientHandle) != IOTHUB_CLIENT_OK)
       {
           printf("ERROR: IoTHubModuleClient_LL_SetModuleTwinCallback(default)..........FAILED!\r\n");
           ret = MU_FAILURE;
       }
       else
       {
           ret = 0;
       }

       return ret;
   }
   ```

1. Speichern Sie die Datei *main.c*.

1. Öffnen Sie die Datei *deployment.template.json*.

1. Fügen Sie den Modulzwilling **CModule** zum Bereitstellungsmanifest hinzu. Fügen Sie am Ende des Abschnitts `moduleContent` nach dem Modulzwilling `$edgeHub` den folgenden JSON-Inhalt ein:

   ```json
   "CModule": {
       "properties.desired":{
           "TemperatureThreshold":25
       }
   }
   ```

   ![Screenshot: Hinzufügen des Modulzwillings zur Bereitstellungsvorlage](./media/tutorial-c-module-windows/module-twin.png)

1. Speichern Sie die Datei *deployment.template.json*.

## <a name="build-and-push-your-module"></a>Erstellen und Pushen Ihres Moduls

Im obigen Abschnitt haben Sie eine IoT Edge-Projektmappe erstellt und **CModule** Code hinzugefügt, mit dem Nachrichten herausgefiltert werden, deren gemeldete Computertemperatur unter dem zulässigen Schwellenwert liegt. Nun müssen Sie die Projektmappe als Containerimage erstellen und per Push an die Containerregistrierung übertragen.

### <a name="sign-in-to-docker"></a>Anmelden bei Docker

Geben Sie Ihre Anmeldeinformationen für die Containerregistrierung für Docker auf Ihrem Entwicklungscomputer an, damit Ihr Containerimage per Pushvorgang übertragen und in der Registrierung gespeichert werden kann.

1. Öffnen Sie PowerShell oder ein Eingabeaufforderungsfenster.

1. Melden Sie sich an Docker mit den Anmeldeinformationen für die Azure-Containerregistrierung an, die Sie nach dem Erstellen der Registrierung gespeichert haben.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Unter Umständen wird eine Sicherheitswarnung angezeigt, in der die Verwendung von `--password-stdin` empfohlen wird. Dies ist zwar eine empfohlene bewährte Methode für Produktionsszenarien, aber sie ist nicht Gegenstand dieses Tutorials. Weitere Informationen finden Sie in der [docker login-Referenz](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin).

### <a name="build-and-push"></a>Erstellen und Pushen

Ihr Entwicklungscomputer hat jetzt Zugriff auf Ihre Containerregistrierung, und dies gilt auch für Ihre IoT Edge-Geräte. Der Projektcode kann nun in ein Containerimage umgewandelt werden.

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf den Namen des Projekts, das Sie erstellen möchten. Der Standardname lautet **AzureIotEdgeApp1**. Für dieses Tutorial haben wir uns für den Namen **CTutorialApp** entschieden und wenn Sie ein Windows-Modul erstellen, sollte die Erweiterung **Windows.Amd64** sein.

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

1. Zeigen Sie die Nachrichten an, die auf Ihrem IoT-Hub eintreffen. Unter Umständen dauert es eine Weile, bis die Nachrichten eintreffen, weil das IoT Edge-Gerät erst seine neue Bereitstellung empfangen und alle Module starten muss. Die Änderungen am CModule-Code müssen warten, bis die Computertemperatur 25 Grad erreicht hat. Erst dann können die Nachrichten gesendet werden. Außerdem wird mit dem Code allen Nachrichten, für die dieser Temperaturschwellenwert erreicht wird, der Nachrichtentyp **Alert** (Warnung) hinzugefügt.

   ![Screenshot: Anzeige von Nachrichten, die auf dem IoT-Hub eintreffen, im Ausgabefenster](./media/tutorial-c-module-windows/view-d2c-message.png)

## <a name="edit-the-module-twin"></a>Bearbeiten des Modulzwillings

Sie haben den Modulzwilling „CModule“ verwendet, um den Temperaturschwellenwert auf 25 Grad festzulegen. Mithilfe des Modulzwillings können Sie die Funktionalität ändern, ohne den Modulcode aktualisieren zu müssen.

1. Öffnen Sie in Visual Studio die Datei *deployment.windows-amd64.json*. 

   Öffnen Sie *nicht* die Datei *deployment.template*. Falls das Bereitstellungsmanifest nicht in der Datei *config* im Projektmappen-Explorer angezeigt wird, sollten Sie in der Symbolleiste des Projektmappen-Explorers das Symbol **Alle Dateien anzeigen** auswählen.

1. Suchen Sie nach dem Zwilling „CModule“, und ändern Sie den Wert des Parameters **temperatureThreshold** in eine neue Temperatur, die fünf bis zehn Grad über der zuletzt gemeldeten Temperatur liegt.

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
