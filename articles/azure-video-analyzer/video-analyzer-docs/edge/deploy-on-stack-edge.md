---
title: Bereitstellen von Azure Video Analyzer unter Azure Stack Edge
description: In diesem Artikel wird erläutert, wie Sie Azure Video Analyzer unter Azure Stack Edge bereitstellen.
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 023d6500a16d5b808bbb2e249076eae9b53741f8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487636"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Bereitstellen von Azure Video Analyzer unter Azure Stack Edge

Dieser Artikel enthält vollständige Anweisungen zum Bereitstellen von Azure Video Analyzer auf Ihrem Azure Stack Edge-Gerät. Nachdem Sie das Gerät eingerichtet und aktiviert haben, ist es für die Video Analyzer-Bereitstellung bereit. 

In dem Artikel stellen wir Video Analyzer unter Verwendung von Azure IoT Hub bereit, doch die Azure Stack Edge-Ressourcen machen eine Kubernetes-API verfügbar, mit der Sie zusätzliche, nicht IoT Hub-fähige Lösungen bereitstellen können, die mit Video Analyzer verbunden werden können. 

> [!TIP]
> Die Verwendung der Kubernetes-API für die benutzerdefinierte Bereitstellung ist ein komplexerer Fall. Wir empfehlen, dass Sie Edgemodule erstellen und sie über IoT Hub auf jeder Azure Stack Edge-Ressource bereitstellen, anstatt die Kubernetes-API hierfür zu verwenden. In diesem Artikel wird gezeigt, wie Sie das Video Analyzer-Modul mithilfe von IoT Hub bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Video Analyzer-Konto

    Dieser [Clouddienst](../overview.md) wird zum Registrieren des Video Analyzer-Edgemoduls und zum Wiedergeben aufgezeichneter Videos und Videoanalysen verwendet.
* Eine verwaltete Identität

    Dies ist die benutzerseitig zugewiesene [verwaltete Identität](../../../active-directory/managed-identities-azure-resources/overview.md), die Sie zum Verwalten des Zugriffs auf Ihr Speicherkonto verwenden.
* Eine [Azure Stack Edge](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md)-Ressource
* Ein [IoT-Hub](../../../iot-hub/iot-hub-create-through-portal.md)
* Ein Speicherkonto

    Es wird empfohlen, ein [allgemeines Speicherkonto der Version 2](../../../storage/common/storage-account-upgrade.md?tabs=azure-portal) zu verwenden.  
* Auf Ihrem Entwicklungscomputer installiertes [Visual Studio Code](https://code.visualstudio.com/)
*  Die in VIsual Studio Code installierte [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
* Vergewissern Sie sich, dass für das Netzwerk, mit dem der Entwicklungscomputer verbunden ist, Advanced Message Queueing Protocol über Port 5671 zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.

## <a name="configure-azure-stack-edge-to-use-video-analyzer"></a>Konfigurieren von Azure Stack Edge für die Verwendung von Video Analyzer

Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Weitere Informationen finden Sie unter [Azure Stack Edge und ausführliche Setupanweisungen ](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md). 

Gehen Sie wie folgt vor, um zu beginnen:

1. [Erstellen Sie eine Azure Stack Edge- oder Azure Data Box Gateway-Ressource](../../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).  
1. [Installieren Sie Azure Stack Edge Pro mit GPU, und richten Sie es ein](../../../databox-online/azure-stack-edge-gpu-deploy-install.md).  
1. Verbinden und aktivieren Sie die Ressource wie folgt:

    a. [Herstellen einer Verbindung mit der lokalen Webbenutzeroberfläche](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
    b. [Konfigurieren des Netzwerks](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
    c. [Konfigurieren des Geräts](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md).  
    d. [Konfigurieren des Zertifikats](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
    e. [Aktivieren Sie das Gerät](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md).  

1. [Anfügen eines IoT-Hubs an Azure Stack Edge](../../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute).

### <a name="meet-the-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Erfüllen der Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge

Bevor Sie fortfahren, stellen Sie sicher, dass Sie Folgendes durchgeführt haben:

* Sie haben die Azure Stack Edge-Ressource aktiviert.
* Sie haben Zugriff auf ein Windows-Clientsystem, auf dem PowerShell 5.0 oder höher ausgeführt wird, um auf die Azure Stack Edge-Ressource zuzugreifen.
* Zum Bereitstellen von Kubernetes-Clustern haben Sie Ihre Azure Stack Edge-Ressource auf der [lokalen Webbenutzeroberfläche](../../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) konfiguriert. 

    1. Verbinden und konfigurieren Sie die Ressource wie folgt: a. [Herstellen einer Verbindung mit der lokalen Webbenutzeroberfläche](../../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
        b. [Konfigurieren des Netzwerks](../../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
        c. [Konfigurieren des Geräts](../../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)  
        d. [Konfigurieren des Zertifikats](../../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
        e. [Aktivieren Sie das Gerät](../../../databox-online/azure-stack-edge-gpu-deploy-activate.md).

    1. Um Compute zu aktivieren, wechseln Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zur Seite **Compute**.
    
        a. Wählen Sie eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten, und wählen Sie dann **Aktivieren** aus. Durch das Aktivieren von Compute wird auf Ihrem Gerät an dieser Netzwerkschnittstelle ein virtueller Switch erstellt.  
        b. Lassen Sie die IP-Adressen der Kubernetes-Testknoten und der externen Kubernetes-Dienste leer.  
        c. Wählen Sie **Übernehmen**. Der Vorgang sollte etwa zwei Minuten dauern.
        
        > [!div class="mx-imgBorder"]

        > :::image type="content" source="../../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Screenshot der Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge.":::

        Wenn für die Kubernetes-API und Azure Stack Edge-Ressource Azure DNS nicht konfiguriert ist, können Sie Ihre Windows-Hostdatei wie folgt aktualisieren:
        
        a. Öffnen Sie als Administrator einen Text-Editor.  
        b. Öffnen Sie die Datei *hosts* unter *C:\Windows\System32\Drivers\etc\\* .  
        c. Fügen Sie der Datei das Internetprotokoll, Version 4 (IPv4), und den Hostnamen des Kubernetes-API-Gerätenamens hinzu. Diese Informationen finden Sie im Azure Stack Edge-Portal unter **Geräte**.  
        d. Speichern und schließen Sie die Datei.

### <a name="deploy-video-analyzer-edge-modules-by-using-the-azure-portal"></a>Bereitstellen von Video Analyzer-Edgemodulen über das Azure-Portal

Im Azure-Portal können Sie ein Bereitstellungsmanifest erstellen und die Bereitstellung auf ein IoT Edge-Gerät pushen.  

#### <a name="select-your-device-and-set-modules"></a>Auswählen des Geräts und Festlegen von Modulen

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an, und wechseln Sie zu Ihrem IoT-Hub.
1. Wählen Sie im linken Bereich die Option **IoT Edge** aus.
1. Wählen Sie in der Liste der Geräte die ID des Zielgeräts aus.
1. Wählen Sie **Module festlegen** aus.

#### <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Das Azure-Portal enthält einen Assistenten, der Sie durch die Erstellung eines Bereitstellungsmanifests führt. Es handelt sich um drei Schritte, die in den Registerkarten **Module**, **Routen** und **Überprüfen + erstellen** organisiert sind.

#### <a name="add-modules"></a>Hinzufügen von Modulen

1. Wählen Sie im Abschnitt **IoT Edge-Module** in der Dropdownliste **Hinzufügen** die Option **IoT Edge-Modul** aus, um die Seite **IoT Edge-Modul hinzufügen** anzuzeigen.
1. Wählen Sie die Registerkarte **Moduleinstellungen** aus, geben Sie einen Namen für das Modul an sowie den URI für das Containerimage. Die folgende Abbildung zeigt Beispielwerte:     
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="Screenshot des Bereichs „Moduleinstellungen“ auf der Seite „IoT Edge-Modul hinzufügen“.":::
    
    > [!TIP]
    > Wählen Sie erst dann **Hinzufügen** aus, wenn Sie auf den Registerkarten **Moduleinstellungen**, **Optionen für Containererstellung** und **Einstellungen für Modulzwilling** Werte eingegeben haben, wie in dieser Vorgehensweise beschrieben.
    
    > [!IMPORTANT]
    > Bei Modulaufrufen beachten Azure IoT Edge-Werte die Groß-/Kleinschreibung. Notieren Sie sich die genaue Zeichenfolge, die Sie als Modulnamen verwenden.
1. Wählen Sie die Registerkarte **Umgebungsvariablen** aus, und geben Sie dann die Werte ein, wie in der folgenden Abbildung gezeigt:
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Screenshot des Bereichs „Umgebungsvariablen“ auf der Seite „Add IoT Edge-Modul hinzufügen“.":::
1. Wählen Sie die Registerkarte **Optionen für Containererstellung** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Screenshot des Bereichs „Optionen für Containererstellung“ auf der Seite „Add IoT Edge-Modul hinzufügen“.":::
 
    Fügen Sie den folgenden JSON-Code in das Feld im Bereich **Optionen für Containererstellung** ein. Diese Aktion beschränkt die Größe der Protokolldateien, die vom Modul erstellt werden.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   Der Abschnitt „Binds“ (Bindungen) im JSON-Code enthält zwei Einträge:
   * **„/var/lib/videoanalyzer:/var/lib/videoanalyzer“** wird verwendet, um die persistenten Anwendungskonfigurationsdaten aus dem Container zu binden und auf dem Edgegerät zu speichern.
   * **"/var/media:/var/media"** bindet die Medienordner zwischen dem Edge-Gerät und dem Container. Wird zum Speichern der Videoaufzeichnungen verwendet, wenn Sie eine Pipelinetopologie ausführen, die das Speichern von Videoclips auf dem Edgegerät unterstützt.
   
1. Wählen Sie die Registerkarte **Einstellungen für Modulzwilling** aus.
 
   Für die Ausführung benötigt das Video Analyzer-Edgemodul eine Reihe obligatorischer Zwillingseigenschaften, die unter [Konfigurationsschema für Modulzwillinge](module-twin-configuration-schema.md) aufgeführt sind. 
1. Fügen Sie den folgenden JSON-Code in das Feld im Bereich **Einstellungen für Modulzwilling** ein:    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
        ...
    }
    ```
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Screenshot des Bereichs „Einstellungen für Modulzwilling“ auf der Seite „IoT Edge-Modul hinzufügen“.":::   

    Um die Überwachung des Moduls zu unterstützen, können Sie dem JSON-Code die folgenden *empfohlenen* Eigenschaften hinzufügen. Weitere Informationen finden Sie unter [Überwachung und Protokollierung](monitor-log-edge.md).
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Wählen Sie **Hinzufügen**.  

#### <a name="add-the-real-time-streaming-protocol-rtsp-simulator-edge-module"></a>Hinzufügen des Edgemoduls RTSP-Simulator (Real-Time Streaming Protocol)

1. Wählen Sie im Abschnitt **IoT Edge-Module** in der Dropdownliste **Hinzufügen** die Option **IoT Edge-Modul** aus, um die Seite **IoT Edge-Modul hinzufügen** anzuzeigen.
1. Wählen Sie die Registerkarte **Moduleinstellungen** aus, geben Sie einen Namen für das Modul an sowie den URI für das Containerimage. Zum Beispiel:   
    
    * **Name des IoT Edge-Moduls**: rtspsim  
    * **Image URI**: mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2 

1. Wählen Sie die Registerkarte **Optionen für Containererstellung** aus, und fügen Sie dann den folgenden JSON-Code in das Feld ein:
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. Wählen Sie **Hinzufügen**.  
1. Klicken Sie auf **Weiter: Routen**, um mit dem Abschnitt über Routen fortzufahren. 
1. Um Routen anzugeben, geben Sie unter **Name** den Namen **AVAToHub** und dann unter **Wert** (Value) den Wert **FROM /messages/modules/avaedge/outputs/ INTO $upstream** ein.
1. Wählen Sie **Weiter: Überprüfen und erstellen** aus, um mit dem Abschnitt zur Überprüfung fortzufahren.
1. Überprüfen Sie Ihre Bereitstellungsinformationen, und wählen Sie anschließend die Option **Erstellen** aus, um das Modul bereitzustellen.

#### <a name="generate-the-provisioning-token"></a>Generieren des Bereitstellungstokens

1. Wechseln Sie im Azure-Portal zu Video Analyzer.
1. Wählen Sie im linken Bereich **Edgemodule** aus.
1. Wählen Sie das Edgemodul aus und dann die Schaltfläche **Token generieren**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Screenshot des Bereichs „Edgemodule hinzufügen“ zum Generieren eines Tokens." lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. Kopieren Sie das Bereitstellungstoken, wie in der folgenden Abbildung gezeigt:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Screenshot der Seite „Bereitstellungstoken kopieren“.":::



#### <a name="optional-set-up-docker-volume-mounts"></a>(Optional) Einrichten von Docker-Volumeeinbindungen

Wenn Sie die Daten in den Arbeitsverzeichnissen anzeigen möchten, richten Sie Docker-Volumeeinbindungen ein, bevor Sie sie bereitstellen. 

Dieser Abschnitt behandelt das Erstellen eines Gatewaybenutzers und das Einrichten von Dateifreigaben zum Anzeigen der Inhalte des Video Analyzer-Arbeitsverzeichnisses und -Medienordners.

> [!NOTE]
> Bindungseinbindungen werden unterstützt, aber mit Volumeeinbindungen können die Daten angezeigt und ggf. remote kopiert werden. Es ist möglich, sowohl Bindungs- als auch Volumeeinbindungen zu verwenden, doch diese können nicht auf denselben Containerpfad zeigen.

1. Wechseln Sie im Azure-Portal zur Azure Stack Edge-Ressource.
1. Erstellen Sie wie folgt einen Gatewaybenutzer, der auf Freigaben zugreifen kann:
    
    a. Wählen Sie im linken Bereich **Cloudspeichergateway** aus.  
    b. Wählen Sie im linken Bereich **Benutzer** aus.  
    c. Wählen Sie **Benutzer hinzufügen** aus, um den Benutzernamen (z. B. wird *avauser* empfohlen) und das Kennwort festzulegen.  
    d. Wählen Sie **Hinzufügen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Screenshot der Azure Stack Edge-Ressourcenseite „Benutzer hinzufügen“.":::
1. Erstellen Sie wie folgt eine *lokale Freigabe* für die Persistenz von Video Analyzer:

    a. Wählen Sie **Cloudspeichergateway** > **Freigaben** aus.  
    b. Wählen Sie **Freigabe hinzufügen** aus.  
    c. Legen Sie einen Freigabenamen fest (z. B. wird *ava* empfohlen).  
    d. Behalten Sie den Freigabetyp **SMB** bei.  
    e. Stellen Sie sicher, dass das Kontrollkästchen **Freigabe mit Edgecomputing verwenden** aktiviert ist.  
    f. Stellen Sie sicher, dass das Kontrollkästchen **Als lokale Edgefreigabe konfigurieren** aktiviert ist.  
    g. Gewähren Sie unter **Benutzerdetails** dem zuletzt erstellten Benutzer Zugriff auf die Freigabe, indem Sie **Vorhandene verwenden** auswählen.  
    h. Klicken Sie auf **Erstellen**.
            
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Screenshot der Seite „Freigabe hinzufügen“ zum Erstellen einer lokalen Freigabe.":::  
    
    > [!TIP]
    > Wenn Ihr Windows-Client mit Ihrem Azure Stack Edge-Gerät verbunden ist, befolgen Sie die Anweisungen im Abschnitt „Verbinden mit einer SMB-Freigabe“ von [Übertragen von Daten mit Azure Stack Edge Pro FPGA](../../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    
1. Erstellen Sie wie folgt eine *Remotefreigabe* für den Dateisynchronisierungsspeicher:

    a. Erstellen Sie ein Azure Blob Storage-Konto in derselben Region, indem Sie **Cloudspeichergateway** > **Speicherkonten** auswählen.  
    b. Wählen Sie **Cloudspeichergateway** > **Freigaben** aus.  
    c. Wählen Sie **Freigaben hinzufügen** aus.  
    d. Geben Sie im Feld **Name** einen Freigabenamen ein (z. B. wird *media* empfohlen).  
    e. Behalten Sie als **Typ** den Freigabetyp **SMB** bei.  
    f. Stellen Sie sicher, dass das Kontrollkästchen **Freigabe mit Edgecomputing verwenden** aktiviert ist.  
    g. Stellen Sie sicher, dass das Kontrollkästchen **Als lokale Edgefreigabe konfigurieren** deaktiviert ist.  
    h. Wählen Sie in der Dropdownliste **Speicherkonto** das zuletzt erstellte Speicherkonto aus.  
    i. Wählen Sie in der Dropdownliste **Speicherdienst** die Option **Blockblob** aus.  
    j. Geben Sie im Feld **Blobcontainer auswählen** den Containernamen ein.  
    k. Wählen Sie unter **Benutzerdetails** die Option **Vorhandene verwenden** aus, um dem zuletzt erstellten Benutzer Zugriff auf die Freigabe zu gewähren.  
    l. Klicken Sie auf **Erstellen**.    
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Screenshot der Seite „Freigabe hinzufügen“ zum Erstellen einer Remotefreigabe.":::

1. Um Volumeeinbindungen zu verwenden, aktualisieren Sie die Einstellungen im Bereich **Optionen für Containererstellung** für das RTSP-Simulatormodul wie folgt:

    a. Wählen Sie die Schaltfläche **Module festlegen** aus.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Screenshot der Schaltfläche „Module festlegen“ im Bereich „Edgegeräteeinstellungen“." lightbox="./media/deploy-on-stack-edge/set-modules.png":::  

    b. Wählen Sie in der Liste **Name** das Modul **rtspsim** aus:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Screenshot des Moduls „rtspsim“ unter „IoT Edge-Module“ im Bereich „Edgegeräteeinstellungen“.":::  
    
    c. Wählen Sie im Bereich **IoT Edge-Modul aktualisieren** die Registerkarte **Optionen für Containererstellung** aus, und fügen Sie dann die Einbindungen hinzu, wie im folgenden JSON-Code gezeigt:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Screenshot des JSON-Einbindungencodes im Bereich „Optionen für Containererstellung“.":::

    ```json
        "createOptions": 
        {
            "HostConfig": 
            {
                "Mounts": 
                [
                    {
                        "Target": "/live/mediaServer/media",
                        "Source": "media",
                        "Type": "volume"
                    }
                ],
                "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
                }
            }
        }
    ```  
    d. Wählen Sie **Aktualisieren** aus.  
    e. Um das Modul zu aktualisieren, wählen Sie **Überprüfen und erstellen** und dann **Erstellen** aus.
    
### <a name="verify-that-the-module-is-running"></a>Überprüfen, ob das Modul ausgeführt wird

Stellen Sie abschließend sicher, dass Ihr IoT Edge-Gerätemodul verbunden ist und wie erwartet ausgeführt wird. Führen Sie die folgenden Schritte aus, um den Laufzeitstatus des Moduls zu überprüfen:

1. Kehren Sie im Azure-Portal zu Ihrer Azure Stack Edge-Ressource zurück.
1. Wählen Sie im linken Bereich **Module** aus. 
1. Wählen Sie im Bereich **Module** in der Liste **Name** das von Ihnen bereitgestellte Modul aus. Der Laufzeitstatus des Moduls in der Spalte **Laufzeitstatus** sollte *Wird ausgeführt* lauten.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Screenshot des Bereichs „Modul“ mit angezeigtem Laufzeitstatus des ausgewählten Moduls als „Wird ausgeführt“." lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurieren der Azure IoT Tools-Erweiterung

Um mittels der Azure IoT Tools-Erweiterung eine Verbindung mit Ihrem IoT-Hub herzustellen, gehen Sie wie folgt vor:

1. Wählen Sie in Visual Studio Code die Option **Ansicht** > **Explorer** aus.
1. Wählen Sie unten links auf der Registerkarte **Explorer** die Option **Azure IoT Hub** aus.
1. Wählen Sie das Symbol **Weitere Optionen** aus, um das Kontextmenü anzuzeigen, und wählen Sie dann **IoT Hub-Verbindungszeichenfolge festlegen** aus.

   Es wird ein Eingabefeld angezeigt, in das Sie Ihre IoT Hub-Verbindungszeichenfolge eingeben. Führen Sie zum Abrufen der Verbindungszeichenfolge folgende Schritte aus: 

   a. Navigieren Sie im Azure-Portal zu Ihrem IoT Hub.  
   b. Wählen Sie im linken Bereich **freigegebene Zugriffsrichtlinien**.  
   c. Wählen Sie **iothubowner get the shared access keys** aus.  
   d. Kopieren Sie den Primärschlüssel der Verbindungszeichenfolge, und fügen Sie ihn dann in das Eingabefeld ein.

   > [!NOTE]
   > Die Verbindungszeichenfolge ist in folgendem Format geschrieben:
   >
   > `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Wenn die Verbindung erfolgreich hergestellt wird, wird eine Liste der Edgegeräte angezeigt, einschließlich Ihres Azure Stack Edge-Geräts. Sie können nun über das Kontextmenü Ihre IoT Edge-Geräte verwalten und mit Azure IoT Hub interagieren. 
   
   Um die auf dem Edgegerät bereitgestellten Module anzuzeigen, erweitern Sie unter dem Azure Stack-Gerät den Knoten **Module**.
    
## <a name="troubleshooting"></a>Problembehandlung

* **Kubernetes-API-Zugriff (kubectl)**

    * Konfigurieren Sie Ihren Computer für den Zugriff auf den Kubernetes-Cluster, indem Sie die Anweisungen unter [Erstellen und Verwalten eines Kubernetes-Clusters auf einem Azure Stack Edge Pro GPU-Gerät](../../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md) befolgen.
    * Alle bereitgestellten IoT Edge-Module verwenden den Namespace *iotedge*. Achten Sie darauf, diesen Namen bei Verwendung von kubectl einzubeziehen. 
* **Modulprotokolle**

    Wenn Sie zum Abrufen von Protokollen nicht auf das *iotedge*-Tool zugreifen können, verwenden Sie den Befehl [kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs), um die Protokolle anzuzeigen oder in eine Datei umzuleiten. Zum Beispiel: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Pod- und Knotenmetriken**

    Verwenden Sie den Befehl [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top), um Pod- und Knotenmetriken anzuzeigen. Beispiel:   <br/>`kubectl top pods -n iotedge` 
* **Modulnetzwerke**   

    Zur Modulermittlung in Azure Stack Edge muss für das Modul in „createOptions“ die Hostportbindung festgelegt sein. Das Modul ist dann über `moduleName:hostport` adressierbar.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```    
* **Volumeeinbindung**

    Ein Modul kann nicht gestartet werden, wenn der Container versucht, ein Volume in ein vorhandenes und nicht leeres Verzeichnis einzubinden.
* **Gemeinsam genutzter Arbeitsspeicher bei Verwendung von gRPC**

    Gemeinsam genutzter Arbeitsspeicher in Azure Stack Edge-Ressourcen wird bei Verwendung von Host-IPC podübergreifend in jedem Namespace unterstützt.
    
    Konfigurieren Sie gemeinsam genutzten Arbeitsspeicher in einem Edgemodul mithilfe des folgenden Codes für die Bereitstellung über IoT Hub:
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a Kubernetes pod or deployment manifest for deployment via the Kubernetes API spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(Erweitert) Gemeinsame Anordnung von Pods**

    Wenn Sie Kubernetes verwenden, um benutzerdefinierte Rückschlusslösungen bereitzustellen, die über gRPC mit Video Analyzer kommunizieren, stellen Sie sicher, dass die Pods auf denselben Knoten wie Video Analyzer-Module bereitgestellt werden.

    * **Option 1**: Verwenden von *Knotenaffinität* und integrierten Knotenbezeichnungen für die gemeinsame Anordnung.  

    Die benutzerdefinierte NodeSelector-Konfiguration scheint derzeit keine Option zu sein, da die Benutzer über keinen Zugriff zum Festlegen von Bezeichnungen für die Knoten verfügen. Abhängig von der Topologie und den Benennungskonventionen des Kunden können sie jedoch möglicherweise [integrierte Knotenbezeichnungen](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels) verwenden. Um eine gemeinsame Anordnung zu erreichen, können Sie dem Rückschlusspodmanifest einen Abschnitt „nodeAffinity“ hinzufügen, der auf Azure Stack Edge-Ressourcen mit Video Analyzer verweist.    
    * **Option 2**: (Empfohlen) Verwenden von *Podaffinität* für die gemeinsame Anordnung.  

        Kubernetes unterstützt [Podaffinität](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity), mit der Pods auf demselben Knoten geplant werden können. Um eine gemeinsame Anordnung zu erreichen, kann dem Rückschlusspodmanifest der Abschnitt „podAffinity“ hinzugefügt werden, der auf das Video Analyzer-Modul verweist.

        ```yaml
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```
* **Sie erhalten einen 404-Fehlercode, wenn Sie das Modul *rtspsim* verwenden**.  
    
    Der Container liest Videos aus genau einem Ordner innerhalb des Containers. Wenn Sie einen externen Ordner einem Ordner zuweisen bzw. an einen solchen Ordner binden, der im Containerimage bereits vorhanden ist, blendet Docker die Dateien aus, die im Containerimage vorhanden sind.
 
    Ohne Bindungen könnte der Container beispielsweise die folgenden Dateien aufweisen:  

    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Und Ihr Host verfügt möglicherweise über die folgenden Dateien:

    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Wenn jedoch die folgende Bindung in der Bereitstellungsmanifestdatei hinzugefügt wird, überschreibt Docker die Inhalte von „/live/mediaServer/media“, um eine Übereinstimmung mit den Inhalten auf dem Host herzustellen.

    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Nächste Schritte

Analysieren von Videos mit maschinellem Sehen und räumlicher Analyse [mithilfe von Azure Stack Edge](computer-vision-for-spatial-analysis.md)
