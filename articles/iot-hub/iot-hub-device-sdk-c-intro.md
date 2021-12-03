---
title: Das Azure IoT-Geräte-SDK für C | Microsoft-Dokumentation
description: Hier finden Sie erste Schritte mit dem Azure IoT-Geräte-SDK für C sowie Informationen zum Erstellen von Geräte-Apps, die mit einer IoT Hub-Instanz kommunizieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: lizross
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: cfbbae0cd7fa54b3403b52d1cced5e81f4df7438
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556019"
---
# <a name="azure-iot-device-sdk-for-c"></a>Azure IoT-Geräte-SDK für C

Das **Azure IoT-Geräte-SDK** ist ein Satz von Bibliotheken, die dazu dienen, den Versand von Nachrichten an den **Azure IoT Hub**-Dienst sowie den Empfang von Nachrichten dieses Diensts zu vereinfachen. Es gibt verschiedene Varianten des SDK für die unterschiedlichen Plattformen. In diesem Artikel wird aber das **Azure IoT-Geräte-SDK für C** beschrieben.

> [!NOTE]
> Das Embedded C SDK ist eine Alternative für eingeschränkte Geräte, bei denen der Ansatz „Bring Your Own Network“ (BYON) unterstützt wird. IoT-Entwickler haben die Möglichkeit, den MQTT-Client, TLS und Socket selbst bereitzustellen, um eine Gerätelösung zu erstellen. [Lesen Sie die weiteren Informationen zum Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Das Azure IoT-Geräte-SDK für C wurde für optimale Portabilität in ANSI C (C99) geschrieben. Dank dieses Features sind die Bibliotheken bestens für den Einsatz auf mehreren Plattformen und Geräten geeignet – insbesondere dann, wenn es wichtig ist, die Größe von Festplatten und Arbeitsspeicher zu reduzieren.

Es gibt eine Vielzahl von Plattformen, auf denen das SDK getestet wurde. (Einzelheiten finden Sie im [Katalog mit für Azure zertifizierten IoT-Geräten](https://devicecatalog.azure.com/).) Der Artikel enthält zwar exemplarische Vorgehensweisen mit Beispielcode, der auf der Windows-Plattform ausgeführt wird, der in diesem Artikel beschriebene Code ist jedoch für alle unterstützten Plattformen identisch.

Das folgende Video bietet eine Übersicht über das Azure IoT SDK für C:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-IoT-C-SDK-insights/Player]

Dieser Artikel bietet einen Einblick in die Architektur des Azure IoT-Geräte-SDKs für C und zeigt, wie Sie die Gerätebibliothek initialisieren, Daten an IoT Hub senden und Nachrichten von IoT Hub empfangen. Die Informationen in diesem Artikel sollten ausreichen, um mit der Arbeit mit dem SDK zu beginnen. Zugleich erhalten Sie Hinweise darauf, wo Sie weitere Informationen zu den Bibliotheken finden können.

## <a name="sdk-architecture"></a>SDK-Architektur

Das [**Azure IoT-Geräte-SDK für C**](https://github.com/Azure/azure-iot-sdk-c) finden Sie im GitHub-Repository und ausführliche Informationen zur API in der [C-API-Referenz](/azure/iot-hub/iot-c-sdk-ref/).

Die neueste Version der Bibliotheken finden Sie im **Mainbranch** des Repositorys:

  ![Screenshot des Mainbranches des Repositorys](./media/iot-hub-device-sdk-c-intro/RepoMasterBranch.png)

* Die Hauptimplementierung des SDKs befindet sich im Ordner **iothub\_client**, der die Implementierung der niedrigsten API-Ebene im SDK enthält: die Bibliothek **IoTHubClient**. Die Bibliothek **IoTHubClient** enthält APIs, die das Messaging zum Senden von Nachrichten an IoT Hub sowie zum Empfangen von Nachrichten von IoT Hub implementieren. Wenn Sie diese Bibliothek verwenden, müssen Sie sich um die Implementierung der Nachrichtenserialisierung kümmern. Andere Details für die Kommunikation mit IoT Hub werden Ihnen jedoch abgenommen.

* Die Bibliothek **IoTHubClient** hängt von anderen Open Source-Bibliotheken ab:

  * Der Bibliothek [Azure C shared utility](https://github.com/Azure/azure-c-shared-utility), die allgemeine Funktionen für grundlegende Aufgaben (etwa Zeichenfolgen, Listenbearbeitung und E/A) bereitstellt, die von mehreren Azure-bezogenen SDKs für C benötigt werden.

  * Der Bibliothek [Azure uAMQP](https://github.com/Azure/azure-uamqp-c). Hierbei handelt es sich um eine clientseitige Implementierung von AMQP, die für Geräte mit eingeschränkten Ressourcen optimiert ist.

  * Der Bibliothek [Azure uMQTT](https://github.com/Azure/azure-umqtt-c). Hierbei handelt es sich um eine allgemeine Bibliothek, die das MQTT-Protokoll implementiert und für Geräte mit eingeschränkten Ressourcen optimiert ist.

Die Verwendung dieser Bibliotheken lässt sich am besten anhand von Beispielcode nachvollziehen. In den folgenden Abschnitten werden einige der im SDK enthaltenen Beispielanwendungen erläutert. Diese exemplarische Vorgehensweise vermittelt einen guten Eindruck von den verschiedenen Funktionen der Architekturebenen des SDKs und gibt einen Überblick über die Funktionsweise der APIs.

## <a name="before-you-run-the-samples"></a>Schritte vor dem Ausführen der Beispiele

Um die Beispiele im Azure IoT-Geräte-SDK für C ausführen zu können, müssen Sie in Ihrem Azure-Abonnement [eine Instanz des IoT Hub-Diensts erstellen](iot-hub-create-through-portal.md). Führen Sie anschließend die folgenden Aufgaben durch:

* Vorbereiten Ihrer Entwicklungsumgebung
* Abrufen der Geräteanmeldeinformationen

### <a name="prepare-your-development-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

Pakete werden für gängige Plattformen (beispielsweise NuGet für Windows oder apt_get für Debian und Ubuntu) bereitgestellt, und in den Beispielen werden diese Pakete verwendet (sofern verfügbar). In einigen Fällen muss das SDK jedoch für Ihr Gerät oder auf Ihrem Gerät kompiliert werden. Informationen zum Kompilieren des SDKs finden Sie bei Bedarf im GitHub-Repository unter [Prepare your development environment](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) (Vorbereiten Ihrer Entwicklungsumgebung).

Den Beispielanwendungscodes erhalten Sie, indem Sie eine Kopie des SDKs von GitHub herunterladen. Rufen Sie Ihre Kopie des Quellcodes aus dem **Mainbranch** des [GitHub-Repositorys](https://github.com/Azure/azure-iot-sdk-c) ab.


### <a name="obtain-the-device-credentials"></a>Abrufen der Geräteanmeldeinformationen

Nachdem Sie nun über den Beispielquellcode verfügen, müssen Sie als Nächstes verschiedene Geräteanmeldeinformationen abrufen. Damit ein Gerät auf einen IoT-Hub zugreifen kann, müssen Sie das Gerät zuerst der IoT Hub-Identitätsregistrierung hinzufügen. Wenn Sie Ihr Gerät hinzufügen, erhalten Sie einen Satz von Geräteanmeldeinformationen. Diese sind erforderlich, damit das Gerät eine Verbindung mit der IoT Hub-Instanz herstellen kann. Für die im nächsten Abschnitt behandelten Beispielanwendungen müssen diese Anmeldeinformationen in Form einer **Geräteverbindungszeichenfolge** vorliegen.

Für die Verwaltung Ihrer IoT Hub-Instanz stehen verschiedene Open Source-Tools zur Verfügung.

* Eine Windows-Anwendung namens [Azure IoT-Explorer](https://github.com/Azure/azure-iot-explorer).

* Eine plattformübergreifende Visual Studio Code-Erweiterung namens [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

* Eine plattformübergreifende Python CLI hat die [IoT-Erweiterung für Azure CLI](https://github.com/Azure/azure-iot-cli-extension) aufgerufen.

In diesem Tutorial wird der grafische *Geräte-Explorer* verwendet. Sie können die *Azure IoT-Tools für VS Code* bei der Entwicklung in VS Code verwenden. Falls Sie lieber mit einem CLI-Tool arbeiten, können Sie auch die *IoT-Erweiterung für Azure CLI 2.0* verwenden.

Der Geräte-Explorer führt mithilfe der Azure IoT-Dienstbibliotheken verschiedene Aufgaben in IoT Hub durch – etwa das Hinzufügen von Geräten. Wenn Sie mithilfe des Geräte-Explorers ein Gerät hinzufügen, erhalten Sie eine Verbindungszeichenfolge für Ihr Gerät. Diese Verbindungszeichenfolge wird zum Ausführen der Beispielanwendungen benötigt.

Sollten Sie noch nicht mit dem Geräte-Explorer vertraut sein, erfahren Sie im folgenden Verfahren, wie Sie mithilfe des Geräte-Explorers ein Gerät hinzufügen und eine Geräteverbindungszeichenfolge beziehen.

1. Informationen zum Installieren des Geräte-Explorers finden Sie unter [How to use Device Explorer for IoT Hub devices](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/tools/) (Verwenden des Geräte-Explorers für IoT Hub-Geräte).

1. Wenn Sie das Programm ausführen, wird die folgende Oberfläche angezeigt:

   ![Device Explorer-Screenshot „Gerätezwilling“](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinConfigTab.png)

1. Geben Sie Ihre **IoT Hub-Verbindungszeichenfolge** in das erste Feld ein, und klicken Sie auf **Aktualisieren**. Mit diesem Schritt wird das Tool für die Kommunikation mit IoT Hub konfiguriert. 

Sie finden die **Verbindungszeichenfolge** unter **IoT Hub-Dienst** > **Einstellungen** > **SAS-Richtlinie** > **iothubowner**.

1. Klicken Sie nach dem Konfigurieren der IoT Hub-Verbindungszeichenfolge auf die Registerkarte **Verwaltung**:

   ![Device Explorer-Screenshot „Gerätezwilling/Verwaltung“](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab.png)

Diese Registerkarte dient zum Verwalten der in Ihrer IoT Hub-Instanz registrierten Geräte.

1. Klicken Sie zum Erstellen eines Geräts auf die Schaltfläche **Erstellen**. Daraufhin wird ein Dialogfeld mit bereits ausgefüllten Schlüsseln (primär und sekundär) angezeigt. Geben Sie eine **Geräte-ID** ein, und klicken Sie anschließend auf **Erstellen**.

   ![Screenshot „Gerät erstellen“](./media/iot-hub-device-sdk-c-intro/CreateDevice.png)

1. Nach Erstellung des Geräts wird die Geräteliste mit allen registrierten Geräten aktualisiert. Dazu gehört auch das soeben erstellte Gerät. Wenn Sie mit der rechten Maustaste auf das neue Gerät klicken, wird das folgende Menü angezeigt:

   ![Ergebnis des Klickens mit der rechten Maustaste im Device Explorer](./media/iot-hub-device-sdk-c-intro/DeviceExplorerTwinManagementTab_RightClick.png)

1. Durch Auswählen von **Copy connection string for selected device** (Verbindungszeichenfolge für das ausgewählte Gerät kopieren) wird die Geräteverbindungszeichenfolge in die Zwischenablage kopiert. Bewahren Sie eine Kopie der Geräteverbindungszeichenfolge auf. Sie wird zum Ausführen der in den nächsten Abschnitten beschriebenen Beispielanwendungen benötigt.

Nach Abschluss der oben genannten Schritte können Sie mit der Codeausführung beginnen. Die meisten Beispiele weisen am Anfang der Hauptquelldatei eine Konstante auf, mit der Sie eine Verbindungszeichenfolge eingeben können. Die entsprechende Zeile aus der Anwendung **iothub_client\_samples\_iothub_convenience_sample** wird beispielsweise wie folgt angezeigt.

```c
static const char* connectionString = "[device connection string]";
```

## <a name="use-the-iothubclient-library"></a>Verwenden der IoTHubClient-Bibliothek

Im Ordner **iothub\_client** des Repositorys [azure-iot-sdk-c](https://github.com/azure/azure-iot-sdk-c) befindet sich der Ordner **samples** mit einer Anwendung namens **iothub\_client\_sample\_mqtt**.

Die Windows-Version der Anwendung **iothub_client\_samples\_iothub_convenience_sample** enthält die folgende Visual Studio-Projektmappe:

  ![Visual Studio-Projektmappen-Explorer](./media/iot-hub-device-sdk-c-intro/iothub-client-sample-mqtt.png)

> [!NOTE]
> Wenn Visual Studio Sie zur Neuausrichtung des Projekts auf die neueste Version auffordert, akzeptieren Sie die Aufforderung.

Die Lösung umfasst ein einziges Projekt. In dieser Projektmappe sind vier NuGet-Pakete installiert:

* Microsoft.Azure.C.SharedUtility
* Microsoft.Azure.IoTHub.MqttTransport
* Microsoft.Azure.IoTHub.IoTHubClient
* Microsoft.Azure.umqtt

Zur Verwendung des SDKs benötigen Sie immer das Paket **Microsoft.Azure.C.SharedUtility** . Da in diesem Beispiel das MQTT-Protokoll verwendet wird, müssen auch die Pakete **Microsoft.Azure.umqtt** und **Microsoft.Azure.IoTHub.MqttTransport** eingeschlossen werden. (Für AMQP und HTTPS sind entsprechende Pakete vorhanden.) Da im Beispiel die Bibliothek **IoTHubClient** verwendet wird, müssen Sie auch das Paket **Microsoft.Azure.IoTHub.IoTHubClient** in die Projektmappe einschließen.

Die Implementierung für die Beispielanwendung befindet sich in der Quelldatei **iothub_client\_samples\_iothub_convenience_sample**.

Anhand dieser Beispielanwendung wird gezeigt, welche Schritte zur Verwendung der Bibliothek **IoTHubClient** erforderlich sind.

### <a name="initialize-the-library"></a>Initialisieren der Bibliothek

> [!NOTE]
> Bevor Sie mit der Verwendung der Bibliotheken beginnen, sind unter Umständen noch plattformspezifische Initialisierungsschritte erforderlich. Wenn Sie z.B. planen, AMQP unter Linux zu verwenden, müssen Sie die OpenSSL-Bibliothek initialisieren. Die Beispiele im [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-c) rufen die Hilfsfunktion **platform\_init** auf, wenn der Client startet, und die Funktion **platform\_deinit**, bevor er beendet wird. Diese Funktionen werden in der Headerdatei „platform.h“ deklariert. Ermitteln Sie anhand der Definitionen dieser Funktionen für Ihre Zielplattform im [Repository](https://github.com/Azure/azure-iot-sdk-c), ob Sie für Ihren Client einen plattformspezifischen Initialisierungscode benötigen.

Um mit der Verwendung der Bibliotheken zu beginnen, ordnen Sie zunächst ein IoT Hub-Clienthandle zu:

```c
if ((iotHubClientHandle = 
  IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol)) == NULL)
{
    (void)printf("ERROR: iotHubClientHandle is NULL!\r\n");
}
else
{
    ...
```

Sie übergeben eine Kopie der aus dem Geräte-Explorer abgerufenen Geräteverbindungszeichenfolge an diese Funktion. Außerdem legen Sie das zu verwendende Kommunikationsprotokoll fest. In diesem Beispiel wird MQTT verwendet, Sie können aber auch AMQP oder HTTPS verwenden.

Wenn Sie ein gültiges **IOTHUB\_CLIENT\_HANDLE** erstellt haben, können Sie damit beginnen, die APIs aufzurufen, um Nachrichten an IoT Hub zu senden und Nachrichten von IoT Hub zu empfangen.

### <a name="send-messages"></a>Senden von Nachrichten

Die Beispielanwendung richtet eine Schleife ein, um Nachrichten an Ihre IoT Hub-Instanz zu senden. Der im Anschluss angegebene Codeausschnitt bewirkt Folgendes:

- Er erstellt eine Nachricht.
- Er fügt der Nachricht eine Eigenschaft hinzu.
- Er sendet eine Nachricht.

Erstellen Sie zunächst eine Nachricht:

```c
size_t iterator = 0;
do
{
    if (iterator < MESSAGE_COUNT)
    {
        sprintf_s(msgText, sizeof(msgText), "{\"deviceId\":\"myFirstDevice\",\"windSpeed\":%.2f}", avgWindSpeed + (rand() % 4 + 2));
        if ((messages[iterator].messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText))) == NULL)
        {
            (void)printf("ERROR: iotHubMessageHandle is NULL!\r\n");
        }
        else
        {
            messages[iterator].messageTrackingId = iterator;
            MAP_HANDLE propMap = IoTHubMessage_Properties(messages[iterator].messageHandle);
            (void)sprintf_s(propText, sizeof(propText), "PropMsg_%zu", iterator);
            if (Map_AddOrUpdate(propMap, "PropName", propText) != MAP_OK)
            {
                (void)printf("ERROR: Map_AddOrUpdate Failed!\r\n");
            }

            if (IoTHubClient_LL_SendEventAsync(iotHubClientHandle, messages[iterator].messageHandle, SendConfirmationCallback, &messages[iterator]) != IOTHUB_CLIENT_OK)
            {
                (void)printf("ERROR: IoTHubClient_LL_SendEventAsync..........FAILED!\r\n");
            }
            else
            {
                (void)printf("IoTHubClient_LL_SendEventAsync accepted message [%d] for transmission to IoT Hub.\r\n", (int)iterator);
            }
        }
    }
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1);

    iterator++;
} while (g_continueRunning);
```

Bei jeder gesendeten Nachricht geben Sie einen Verweis auf eine Rückruffunktion an, die aufgerufen wird, wenn die Daten gesendet werden. In diesem Beispiel heißt die Rückruffunktion **SendConfirmationCallback**. Der folgende Codeausschnitt zeigt diese Rückruffunktion:

```c
static void SendConfirmationCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback)
{
    EVENT_INSTANCE* eventInstance = (EVENT_INSTANCE*)userContextCallback;
    (void)printf("Confirmation[%d] received for message tracking id = %zu with result = %s\r\n", callbackCounter, eventInstance->messageTrackingId, MU_ENUM_TO_STRING(IOTHUB_CLIENT_CONFIRMATION_RESULT, result));
    /* Some device specific action code goes here... */
    callbackCounter++;
    IoTHubMessage_Destroy(eventInstance->messageHandle);
}
```

Beachten Sie den Aufruf der Funktion **IoTHubMessage\_Destroy**, wenn Sie mit der Nachricht fertig sind. Diese Funktion gibt die bei der Erstellung der Nachricht zugeordneten Ressourcen frei.

### <a name="receive-messages"></a>Empfangen von Nachrichten

Der Empfang einer Nachricht ist ein asynchroner Vorgang. Zuerst registrieren Sie den Rückruf, der aufgerufen wird, wenn das Gerät eine Nachricht empfängt:

```c
if (IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext) != IOTHUB_CLIENT_OK)
{
    (void)printf("ERROR: IoTHubClient_LL_SetMessageCallback..........FAILED!\r\n");
}
else
{
    (void)printf("IoTHubClient_LL_SetMessageCallback...successful.\r\n");
    ...
```

Der letzte Parameter ist ein ungültiger Zeiger. In diesem Beispiel handelt es sich um einen Zeiger auf einen ganzzahligen Wert. Es kann jedoch auch ein Zeiger auf eine komplexere Datenstruktur verwendet werden. Dieser Parameter erlaubt es der Rückruffunktion, mit dem Aufrufer dieser Funktion auf der Grundlage eines gemeinsamen Zustands zusammenzuarbeiten.

Wenn das Gerät eine Nachricht empfängt, wird die registrierte Rückruffunktion aufgerufen. Diese Rückruffunktion ruft Folgendes ab:

* Die Nachrichten-ID und die Korrelations-ID aus der Nachricht
* Den Nachrichteninhalt
* Benutzerdefinierte Eigenschaften aus der Nachricht (sofern vorhanden)

```c
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    int* counter = (int*)userContextCallback;
    const char* buffer;
    size_t size;
    MAP_HANDLE mapProperties;
    const char* messageId;
    const char* correlationId;

    // Message properties
    if ((messageId = IoTHubMessage_GetMessageId(message)) == NULL)
    {
        messageId = "<null>";
    }

    if ((correlationId = IoTHubMessage_GetCorrelationId(message)) == NULL)
    {
        correlationId = "<null>";
    }

    // Message content
    if (IoTHubMessage_GetByteArray(message, (const unsigned char**)&buffer, &size) != IOTHUB_MESSAGE_OK)
    {
        (void)printf("unable to retrieve the message data\r\n");
    }
    else
    {
        (void)printf("Received Message [%d]\r\n Message ID: %s\r\n Correlation ID: %s\r\n Data: <<<%.*s>>> & Size=%d\r\n", *counter, messageId, correlationId, (int)size, buffer, (int)size);
        // If we receive the work 'quit' then we stop running
        if (size == (strlen("quit") * sizeof(char)) && memcmp(buffer, "quit", size) == 0)
        {
            g_continueRunning = false;
        }
    }

    // Retrieve properties from the message
    mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                size_t index;

                printf(" Message Properties:\r\n");
                for (index = 0; index < propertyCount; index++)
                {
                    (void)printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                (void)printf("\r\n");
            }
        }
    }

    /* Some device specific action code goes here... */
    (*counter)++;
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Verwenden Sie die Funktion **IoTHubMessage\_GetByteArray**, um die Nachricht abzurufen. In diesem Beispiel handelt es sich dabei um eine Zeichenfolge.

### <a name="uninitialize-the-library"></a>Aufheben der Initialisierung der Bibliothek

Wenn Sie mit dem Senden von Ereignissen und Empfangen von Nachrichten fertig sind, können Sie die Initialisierung der IoT-Bibliothek aufheben. Geben Sie hierzu den folgenden Funktionsaufruf aus:

```c
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Durch diesen Aufruf werden die zuvor von der Funktion **IoTHubClient\_CreateFromConnectionString** zugeordneten Ressourcen freigegeben.

Wie Sie sehen, lassen sich mit der Bibliothek **IoTHubClient** ganz einfach Nachrichten senden und empfangen. Die Bibliothek kümmert sich um die Details für die Kommunikation mit IoT Hub, z. B. die Auswahl des Protokolls (aus Sicht des Entwicklers ist dies eine einfache Konfigurationsoption).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die Grundlagen zur Verwendung von Bibliotheken im **Azure IoT-Geräte-SDK für C** beschrieben. Anhand von Windows-Beispielen haben Sie gelernt, was das SDK umfasst, wie die Architektur gestaltet ist und wie Sie mit der Arbeit beginnen. Im nächsten Artikel wird das SDK näher vorgestellt. Sie erhalten [weitere Informationen zur IoTHubClient-Bibliothek](iot-hub-device-sdk-c-iothubclient.md).

Weitere Informationen zum Entwickeln für IoT Hub finden Sie im Artikel über die [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter:

* [Bereitstellen von KI auf Edge-Geräten mit Azure IoT Edge](../iot-edge/quickstart-linux.md)
