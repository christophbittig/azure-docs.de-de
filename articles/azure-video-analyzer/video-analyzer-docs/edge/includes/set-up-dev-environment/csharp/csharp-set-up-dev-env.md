---
author: russell-cooks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: e2deaa2e40a1924f2f78d095bc59528fe12c6d1e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861107"
---
### <a name="get-the-sample-code"></a>Beispielcode herunterladen

1. Klonen Sie das [Repository mit AVA-C#-Beispielen](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp).
1. Starten Sie Visual Studio Code, und öffnen Sie den Ordner, in den das Repository heruntergeladen wurde.
1. Navigieren Sie in Visual Studio Code zum Ordner „src/cloud-to-device-console-app“, und erstellen Sie eine Datei namens **appsettings.json**. Diese Datei enthält die Einstellungen, die zum Ausführen des Programms erforderlich sind.
1. Navigieren Sie im Speicherkonto, das Sie im obigen Setupschritt erstellt haben, zur Dateifreigabe, und suchen Sie unter der Dateifreigabe „deployment-output“ nach der Datei **appsettings.json**. Klicken Sie auf die Datei und dann auf die Schaltfläche „Herunterladen“. Der Inhalt sollte auf einer neuen Browserregisterkarte geöffnet werden und etwa wie folgt aussehen:

   ```
   {
       "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",
       "deviceId" : "avasample-iot-edge-device",
       "moduleId" : "avaedge"
   }
   ```

   Die IoT Hub-Verbindungszeichenfolge ermöglicht die Verwendung von Visual Studio Code, um Befehle über Azure IoT Hub an die Edge-Module zu senden. Kopieren Sie den obigen JSON-Code in die Datei **src/cloud-to-device-console-app/appsettings.json**.
1. Navigieren Sie als Nächstes zum Ordner „src/edge“, und erstellen Sie eine Datei vom Typ **.env**. Diese Datei enthält Eigenschaften, die Visual Studio Code zum Bereitstellen von Modulen auf einem Edgegerät verwendet.
1. Navigieren Sie im Speicherkonto, das Sie im obigen Setupschritt erstellt haben, zur Dateifreigabe, und suchen Sie unter der Dateifreigabe „deployment-output“ nach der Datei **env.txt**. Klicken Sie auf die Datei und dann auf die Schaltfläche „Herunterladen“. Der Inhalt sollte auf einer neuen Browserregisterkarte geöffnet werden und etwa wie folgt aussehen:

   ```
        SUBSCRIPTION_ID="<Subscription ID>"
        RESOURCE_GROUP="<Resource Group>"
        AVA_PROVISIONING_TOKEN="<Provisioning token>"
        VIDEO_INPUT_FOLDER_ON_DEVICE="/home/localedgeuser/samples/input"
        VIDEO_OUTPUT_FOLDER_ON_DEVICE="/var/media"
        APPDATA_FOLDER_ON_DEVICE="/var/lib/videoAnalyzer"
        CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"
        CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"
   ```

   Kopieren Sie den JSON-Code aus der Datei **env.txt** in die Datei **src/edge/.env**.

### <a name="connect-to-the-iot-hub"></a>Herstellen der Verbindung mit dem IoT Hub

1. Legen Sie in Visual Studio Code die IoT Hub-Verbindungszeichenfolge fest, indem Sie im Bereich **AZURE IOT HUB** in der unteren linken Ecke das Symbol **Weitere Aktionen** auswählen. Kopieren Sie die Zeichenfolge aus der Datei „src/cloud-to-device-console-app/appsettings.json“.

    <!-- commenting out the image for now ![Set IoT Hub connection string]()./media/quickstarts/set-iotconnection-string.png-->
    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
1. Aktualisieren Sie nach ungefähr 30 Sekunden Azure IoT Hub im unteren linken Bereich. Es sollte das Edgegerät `avasample-iot-edge-device` angezeigt werden, auf dem die folgenden Module bereitgestellt sind:
    - Edge-Hub (Modulname **edgeHub**)
    - Edge-Agent (Modulname **edgeAgent**)
    - Video Analyzer (Modulname **avaedge**)
    - RTSP-Simulator (Modulname **rtspsim**)

### <a name="prepare-to-monitor-the-modules"></a>Vorbereiten der Überwachung der Module

Wenn Sie diese Schnellstartanleitung oder dieses Tutorial verwenden, werden Ereignisse an IoT Hub gesendet. Führen Sie zum Anzeigen dieser Ereignisse die folgenden Schritte aus:

1. Öffnen Sie in Visual Studio Code den Explorer-Bereich, und suchen Sie links unten nach **Azure IoT Hub**.
1. Erweitern Sie den Knoten **Geräte**.
1. Klicken Sie mit der rechten Maustaste auf `avasample-iot-edge-device`, und wählen Sie die Option **Überwachung des integrierten Ereignisendpunkts starten** aus.

    [!INCLUDE [provide-builtin-endpoint](../../common-includes/provide-builtin-endpoint.md)]
