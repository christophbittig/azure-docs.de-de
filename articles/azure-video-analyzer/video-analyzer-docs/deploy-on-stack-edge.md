---
title: Bereitstellen von Azure Video Analyzer unter Azure Stack Edge
description: In diesem Artikel sind die Schritte aufgelistet, mit denen Sie Azure Video Analyzer unter Azure Stack Edge bereitstellen können.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 1cfcd7956cd14d0c687c8619732523a5d7bba4c0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605205"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Bereitstellen von Azure Video Analyzer unter Azure Stack Edge

In diesem Artikel sind die Schritte aufgelistet, mit denen Sie Video Analyzer unter Azure Stack Edge bereitstellen können. Nachdem das Gerät eingerichtet und aktiviert wurde, ist es für die Bereitstellung von Video Analyzer bereit. 

Wir stellen Video Analyzer über IoT Hub bereit. Von den Azure Stack Edge-Ressourcen wird aber eine Kubernetes-API verfügbar gemacht, mit der Kunden zusätzliche nicht IoT Hub-fähige Ressourcen bereitstellen können, die mit Video Analyzer verbunden werden können. 

> [!TIP]
> Die Verwendung der Kubernetes (K8s)-API für die benutzerdefinierte Bereitstellung ist ein komplexerer Fall. Es wird empfohlen, statt Verwendung der Kubernetes-API Edge-Module zu erstellen und die Bereitstellung in jeder Azure Stack Edge-Ressource über IoT Hub auszuführen. In diesem Artikel werden die Schritte zum Bereitstellen des Video Analyzer-Moduls mit IoT Hub erläutert.

## <a name="prerequisites"></a>Voraussetzungen

* Video Analyzer-Konto

    Dieser [Clouddienst](./overview.md) wird zum Registrieren des Video Analyzer-Edgemoduls und zum Wiedergeben aufgezeichneter Videos und Videoanalysen verwendet.
* Verwaltete Identität

    Dies ist die benutzerseitig zugewiesene [verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md), die zum Verwalten des Zugriffs auf das obige Speicherkonto verwendet wird.
* Eine [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)-Ressource
* Ein [IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* Speicherkonto

    Es wird empfohlen, Speicherkonten vom Typ „Allgemein V2“ (GPv2) zu verwenden.  
    Weitere Informationen über ein [Speicherkonto vom Typ „Allgemein v2“](../../storage/common/storage-account-upgrade.md?tabs=azure-portal)
* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Vergewissern Sie sich, dass für das Netzwerk, mit dem der Entwicklungscomputer verbunden ist, Advanced Message Queueing Protocol über Port 5671 zulässig ist. Dieses Setup ermöglicht die Kommunikation zwischen Azure IoT Tools und Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>Konfigurieren von Azure Stack Edge für die Verwendung von Video Analyzer

Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Erfahren Sie mehr über [Azure Stack Edge und ausführliche Setupanweisungen ](../../databox-online/azure-stack-edge-gpu-deploy-prep.md). Folgen Sie für den Einstieg den Anweisungen unter den nachstehenden Links:

* [Erstellung von Azure Stack Edge-/Data Box Gateway-Ressourcen](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [Installation und Einrichtung](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* Verbindung und Aktivierung

    1. [Herstellen einer Verbindung](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [Konfigurieren des Netzwerks](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [Konfigurieren des Geräts](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [Konfigurieren von Zertifikaten](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [Aktivieren](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [Anfügen von IoT Hub an Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Aktivieren von Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge

Stellen Sie Folgendes sicher, bevor Sie fortfahren:

* Sie haben die Azure Stack Edge-Ressource aktiviert.
* Sie haben Zugriff auf ein Windows-Clientsystem mit PowerShell 5.0 oder höher, um auf die Azure Stack Edge-Ressource zuzugreifen.
* Zum Bereitstellen eines Kubernetes-Clusters müssen Sie die Azure Stack Edge-Ressource über die [lokale Webbenutzeroberfläche](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup) konfigurieren. 

    * Verbinden und Konfigurieren:
    
        1. [Herstellen einer Verbindung](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [Konfigurieren des Netzwerks](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [Konfigurieren des Geräts](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [Konfigurieren von Zertifikaten](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [Aktivieren](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * Wechseln Sie auf der lokalen Webbenutzeroberfläche des Geräts zur Seite „Compute“, um Compute zu aktivieren.
    
        * Wählen Sie eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten. Wählen Sie „Aktivieren“ aus. Das Aktivieren des Computevorgangs führt zur Erstellung eines virtuellen Switches auf Ihrem Gerät für diese Netzwerkschnittstelle.
        * Lassen Sie die IP-Adressen der Kubernetes-Testknoten und der externen Kubernetes-Dienste leer.
        * Wählen Sie „Anwenden“ aus – dieser Vorgang dauert ca. 2 Minuten.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text=" Computevoraussetzungen auf der lokalen Benutzeroberfläche von Azure Stack Edge":::

        * Wenn für die Kubernetes-API und Azure Stack Edge-Ressource kein DNS konfiguriert ist, können Sie die Windows-Hostdatei aktualisieren.
        
            * Öffnen Sie als Administrator einen Text-Editor.
            * Öffnen Sie die Datei „C:\Windows\System32\Drivers\etc\hosts“.
            * Fügen Sie der Datei die IPv4-Adresse für den Kubernetes-API-Gerätenamen und den Hostnamen hinzu. (Diese Informationen finden Sie im Azure Stack Edge-Portal im Abschnitt „Geräte“.)
            * Speichern und schließen

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>Bereitstellen von Video Analyzer-Edgemodulen über das Azure-Portal

Im Azure-Portal werden Sie durch das Erstellen eines Bereitstellungsmanifests und die Übertragung der Bereitstellung auf ein IoT Edge-Gerät per Pushvorgang geführt.  
#### <a name="select-your-device-and-set-modules"></a>Auswählen des Geräts und Festlegen von Modulen

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Menü **IoT Edge** aus.
1. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.
1. Wählen Sie **Module festlegen** aus.

#### <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Das Azure-Portal enthält einen Assistenten, der Sie durch die Erstellung eines Bereitstellungsmanifests führt. Der Vorgang besteht aus drei Schritten, die auf Registerkarten aufgeteilt sind: **Module**, **Routen** und **Überprüfen und erstellen**.

#### <a name="add-modules"></a>Hinzufügen von Modulen

1. Klicken Sie im Abschnitt **IoT Edge-Module** auf das Dropdownmenü **Hinzufügen**, und wählen Sie **IoT Edge-Modul** aus, um die Seite **IoT Edge-Modul hinzufügen** anzuzeigen.
1. Geben Sie auf der Registerkarte **Moduleinstellungen** einen Namen für das Modul und den URI für das Containerimage an:   
    Beispiele:
    
    * **Name des IoT Edge-Moduls**: avaedge
    * **Image-URI**: mcr.microsoft.com/media/video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="Screenshot: Registerkarte „Moduleinstellungen“":::
    
    > [!TIP]
    > Klicken Sie erst dann auf **Hinzufügen**, wenn Sie auf den Registerkarten **Moduleinstellungen**, **Optionen für Containererstellung** und **Einstellungen für Modulzwilling** Werte eingegeben haben, wie in dieser Vorgehensweise beschrieben.
    
    > [!WARNING]
    > Bei Modulaufrufen für Azure IoT Edge muss die Groß-/Kleinschreibung beachtet werden. Notieren Sie sich die genaue Zeichenfolge, die Sie als Modulnamen verwenden.

1. Öffnen Sie die Registerkarte **Umgebungsvariablen**.
   
   Fügen Sie die folgenden Werte in die angezeigten Eingabefelder ein.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Umgebungsvariablen":::

1. Öffnen Sie die Registerkarte **Optionen für Containererstellung**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Optionen für die Containererstellung":::
 
    Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn in das Feld ein, um die Größe der vom Modul erzeugten Protokolldateien einzuschränken.
    
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
   
   Der Abschnitt „Binds“ im JSON-Code enthält zwei Einträge:
   1. „/var/lib/videoanalyzer:/var/lib/videoanalyzer“: Hiermit werden die persistenten Anwendungskonfigurationsdaten aus dem Container gebunden und auf dem Edgegerät gespeichert.
   1. "/var/media:/var/media": Dadurch werden die Medienordner zwischen dem Edge-Gerät und dem Container gebunden. Wird zum Speichern der Videoaufzeichnungen verwendet, wenn Sie eine Pipelinetopologie ausführen, die das Speichern von Videoclips auf dem Edgegerät unterstützt.
   
1. Kopieren Sie den folgenden JSON-Code, und fügen Sie ihn auf der Registerkarte **Einstellungen für Modulzwilling** in das Feld ein.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Zwillingseinstellungen":::

    Azure Video Analyzer benötigt für die Ausführung eine Reihe obligatorischer Zwillingseigenschaften, die unter [Konfigurationsschema für Modulzwillinge](module-twin-configuration-schema.md) aufgeführt sind.  

    Der JSON-Code, den Sie im Editierfeld „Einstellungen für Modulzwilling“ eingeben müssen, sieht wie folgt aus:    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    Unten finden Sie einige weitere **empfohlene** Eigenschaften, die dem JSON-Code hinzugefügt werden können und die Überwachung des Moduls unterstützen. Weitere Informationen finden Sie unter [Überwachung und Protokollierung](monitor-log-edge.md).
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Klicken Sie auf **Weiter: Routen**, um mit dem Abschnitt über Routen fortzufahren. Geben Sie Routen an.

    Geben Sie unter „NAME“ den Namen **AVAToHub** und unter „VALUE“ den Wert **FROM /messages/modules/avaedge/outputs/ INTO $upstream** ein.
1. Wählen Sie **Weiter: Überprüfen und erstellen** aus, um mit dem Abschnitt zur Überprüfung fortzufahren.
1. Überprüfen Sie Ihre Bereitstellungsinformationen, und wählen Sie anschließend die Option **Erstellen** aus, um das Modul bereitzustellen.

    > [!TIP]
    > Führen Sie die folgenden Schritte aus, um das Bereitstellungstoken zu generieren:
1. Öffnen Sie das Azure-Portal, und navigieren Sie zum Video Analyzer.
1. Klicken Sie im linken Navigationsbereich auf **Edgemodule**.
1. Wählen Sie das Edgemodul aus, und klicken Sie auf die Schaltfläche **Token generieren**:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Generieren des Tokens" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. Kopieren Sie das Bereitstellungstoken:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Kopieren des Tokens":::

#### <a name="optional-setup-docker-volume-mounts"></a>(Optional) Einrichten von Docker-Volumebereitstellungen

Wenn Sie die Daten in den Arbeitsverzeichnissen anzeigen möchten, führen Sie die folgenden Schritte aus, um vor der Bereitstellung die Docker-Volumebereitstellungen einzurichten. 

Diese Schritte umfassen das Erstellen eines Gatewaybenutzers und das Einrichten von Dateifreigaben zum Anzeigen der Inhalte des Video Analyzer-Arbeitsverzeichnisses und -Medienordners.

> [!NOTE]
> Bindungsbereitstellungen werden unterstützt, mit Volumebereitstellungen können jedoch die Daten angezeigt und ggf. remote kopiert werden. Es ist möglich, sowohl Bindungs- als auch Volumebereitstellungen zu verwenden, diese können jedoch nicht auf denselben Containerpfad zeigen.

1. Öffnen Sie das Azure-Portal, und navigieren Sie zu Ihrer Azure Stack Edge-Ressource.
1. Erstellen Sie einen **Gateway-Benutzer**, der auf Freigaben zugreifen kann.
    
    1. Klicken Sie im linken Navigationsbereich auf **Cloudspeichergateway**.
    1. Klicken Sie im linken Navigationsbereich auf **Benutzer**.
    1. Klicken Sie auf **+ Benutzer hinzufügen**, um den Benutzernamen und das Kennwort festzulegen. (Empfohlen: `avauser`).
    1. Klicken Sie auf **Hinzufügen**.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Benutzer hinzufügen":::
1. Erstellen Sie eine **Lokale Freigabe** für die dauerhafte Speicherung der Video Analyzer-Daten.

    1. Klicken Sie auf **Cloudspeichergateway > Freigaben**.
    1. Klicken Sie auf **+ Add Shares**  (+ Freigaben hinzufügen).
    1. Legen Sie einen Freigabenamen fest. (Empfohlen: `ava`).
    1. Behalten Sie den Freigabetyp SMB bei.
    1. Stellen Sie sicher, dass **Freigabe mit Edgecomputing verwenden** aktiviert ist.
    1. Stellen Sie sicher, dass **Als lokale Edge-Freigabe konfigurieren** aktiviert ist.
    1. Gewähren Sie unter „Benutzerdetails“ dem zuletzt erstellten Benutzer Zugriff auf die Freigabe.
    1. Klicken Sie auf **Erstellen**.
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Lokale Freigabe":::  
    
        > [!TIP]
        > Stellen Sie mithilfe des mit Azure Stack Edge verbundenen Windows-Clients eine Verbindung mit den SMB-Freigaben her, indem Sie [die in diesem Dokument beschriebenen Schritte](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) ausführen.    
1. Erstellen Sie eine Remotefreigabe für den Dateisynchronisierungsspeicher.

    1. Erstellen Sie zunächst ein Blob Storage-Konto in derselben Region, indem Sie auf **Cloudspeichergateway > Speicherkonten** klicken.
    1. Klicken Sie auf **Cloudspeichergateway > Freigaben**.
    1. Klicken Sie auf **+ Add Shares**  (+ Freigaben hinzufügen).
    1. Legen Sie einen Freigabenamen fest. (Empfohlen: „media“).
    1. Behalten Sie den Freigabetyp SMB bei.
    1. Stellen Sie sicher, dass **Freigabe mit Edgecomputing verwenden** aktiviert ist.
    1. Stellen Sie sicher, dass **Als lokale Edge-Freigabe konfigurieren** nicht aktiviert ist.
    1. Wählen Sie das zuletzt erstellte Speicherkonto aus.
    1. Legen Sie den Speichertyp auf Blockblob fest.
    1. Legen Sie einen Containernamen fest.
    1. Gewähren Sie unter „Benutzerdetails“ dem zuletzt erstellten Benutzer Zugriff auf die Freigabe.
    1. Klicken Sie auf **Erstellen**.    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Remotefreigabe":::
1. Aktualisieren Sie die Optionen für die Containererstellung des Moduls „RTSP-Simulator“ so, dass Volumebereitstellungen verwendet werden:
    1. Klicken Sie auf die Schaltfläche **Module festlegen**:

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Festlegen von Modulen" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. Klicken Sie auf das Modul **rtspsim**:

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Auswählen des Moduls":::
    1. Wählen Sie die Registerkarte **Optionen für Containererstellung** aus, und fügen Sie die Bereitstellungen wie unten dargestellt hinzu:
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Aktualisieren des Moduls":::

        ```json
            "createOptions": 
            {
                "HostConfig": 
                {
                    "Mounts": 
                    [
                        {
                            "Target": "/var/media",
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
    1. Klicken Sie auf die Schaltfläche **Aktualisieren**.
    1. Klicken Sie auf die Schaltfläche **Überprüfen und erstellen** und abschließend auf die Schaltfläche **Erstellen**, um das Modul zu aktualisieren.
    
### <a name="verify-that-the-module-is-running"></a>Überprüfen, ob das Modul ausgeführt wird

Im letzten Schritt wird sichergestellt, dass das Modul verbunden ist und wie erwartet ausgeführt wird. Der Laufzeitstatus des Moduls sollte für Ihr IoT Edge-Gerät auf der IoT Hub-Ressource „Wird ausgeführt“ lauten.

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob das Modul ausgeführt wird:

1. Kehren Sie im Azure-Portal zur Azure Stack Edge-Ressource zurück.
1. Wählen Sie die Kachel „Module“ aus. Das Blatt Module wird geöffnet. Identifizieren Sie in der Liste mit den Modulen das von Ihnen bereitgestellte Modul. Der Laufzeitstatus des hinzugefügten Moduls sollte Wird ausgeführt lauten.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Benutzerdefiniertes Modul" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Konfigurieren der Azure IoT Tools-Erweiterung

Befolgen Sie die unten angegebene Anleitung, um mit der Azure IoT Tools-Erweiterung eine Verbindung mit Ihrem IoT-Hub herzustellen.

1. Wählen Sie in Visual Studio „Ansicht“ > „Explorer“ aus. Oder drücken Sie STRG+UMSCHALT+E.
1. Wählen Sie unten links auf der Registerkarte Explorer die Option Azure IoT Hub aus.
1. Wählen Sie das Symbol Weitere Optionen aus, um das Kontextmenü anzuzeigen. Wählen Sie anschließend IoT Hub-Verbindungszeichenfolge festlegen aus.
1. Geben Sie Ihre IoT Hub-Verbindungszeichenfolge ein, wenn das entsprechende Eingabefeld angezeigt wird. 

   * Um die Verbindungszeichenfolge abzurufen, navigieren Sie im Azure-Portal zu IoT Hub, und klicken Sie im linken Navigationsbereich auf „Richtlinien für gemeinsamen Zugriff“.
   * Klicken Sie auf „iothubowner“, um die Schlüssel für gemeinsamen Zugriff abzurufen.
   * Kopieren Sie den Primärschlüssel der Verbindungszeichenfolge, und fügen Sie ihn in Visual Studio Code in das Eingabefeld ein.

   Die Verbindungszeichenfolge lautet:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Wenn die Verbindung erfolgreich hergestellt wird, wird die Liste mit den Edgegeräten angezeigt. Azure Stack Edge wird angezeigt. Sie können nun über das Kontextmenü Ihre IoT Edge-Geräte verwalten und mit Azure IoT Hub interagieren. Um die auf dem Edgegerät bereitgestellten Module anzuzeigen, erweitern Sie unter dem Azure Stack-Gerät den Knoten „Module“.
    
## <a name="troubleshooting"></a>Problembehandlung

* **Kubernetes-API-Zugriff (kubectl)**

    * Gehen Sie gemäß der Dokumentation vor, um Ihren Computer für den [Zugriff auf den Kubernetes-Cluster](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md) zu konfigurieren.
    * Alle bereitgestellten IoT Edge Module verwenden den `iotedge`-Namespace. Schließen Sie ihn ein, wenn Sie kubectl verwenden.  
* **Modulprotokolle**

    Auf das Tool `iotedge` kann nicht zugegriffen werden, um Protokolle abzurufen. Sie müssen [kubectl-Protokolle](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) verwenden, um die Protokolle anzuzeigen oder an eine Datei zu übergeben. Beispiel: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Pod- und Knotenmetriken**

    Verwenden Sie den Befehl [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top), um Pod- und Knotenmetriken anzuzeigen.
    <br/>`kubectl top pods -n iotedge` 
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
    
* **Volumebereitstellung**

    Ein Modul kann nicht gestartet werden, wenn der Container versucht, ein Volume in ein vorhandenes und nicht leeres Verzeichnis einzubinden.
* **Gemeinsam genutzter Arbeitsspeicher bei Verwendung von gRPC**

    Gemeinsam genutzter Arbeitsspeicher in Azure Stack Edge-Ressourcen wird mit Host-IPC podübergreifend in jedem Namespace unterstützt.
    Konfigurieren von gemeinsam genutztem Speicher in einem Edgemodul für die Bereitstellung über IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **(Erweitert) Gemeinsame Anordnung von Pods**

    Wenn Sie mit K8s benutzerdefinierte Rückschlusslösungen bereitstellen, die über gRPC mit Video Analyzer kommunizieren, müssen Sie sicherstellen, dass die Pods auf denselben Knoten wie Video Analyzer-Module bereitgestellt werden.

    * **Option 1:** Verwenden von Knotenaffinität und integrierten Knotenbezeichnungen für die gemeinsame Anordnung

    Die benutzerdefinierte NodeSelector-Konfiguration scheint derzeit keine Option zu sein, da die Benutzer über keinen Zugriff zum Festlegen von Bezeichnungen für die Knoten verfügen. Abhängig von der Topologie und den Benennungskonventionen des Kunden können sie jedoch möglicherweise [integrierte Knotenbezeichnungen](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels) verwenden. Um eine gemeinsame Anordnung zu erreichen, kann dem Rückschlusspodmanifest der Abschnitt „nodeAffinity“ hinzugefügt werden, der auf Azure Stack Edge-Ressourcen mit Video Analyzer verweist.
    * **Option 2:** Verwenden von Podaffinität für die gemeinsame Anordnung (empfohlen)

        Da für Kubernetes die [Podaffinität](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity) unterstützt wird, können Pods auf demselben Knoten geplant werden. Um eine gemeinsame Anordnung zu erreichen, kann dem Rückschlusspodmanifest der Abschnitt „podAffinity“ hinzugefügt werden, der auf das Video Analyzer-Modul verweist.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example Inference deployment manifest pod affinity
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
* **404-Fehlercode bei Verwendung des `rtspsim`-Moduls**  
    
    Der Container liest Videos aus genau einem Ordner innerhalb des Containers. Wenn Sie einen externen Ordner einem Ordner zuweisen bzw. an einen Ordner binden, der im Containerimage bereits vorhanden ist, blendet Docker die Dateien aus, die sich im Containerimage befinden.  
 
    Wenn keine Bindungen bestehen, weist ein Container beispielsweise die folgenden Dateien auf:  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Ihr Host verfügt möglicherweise über die folgenden Dateien:
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Wenn jedoch die folgende Bindung in der Bereitstellungsmanifestdatei hinzugefügt wird, überschreibt Docker die Inhalte von /live/mediaServer/media, um für eine Übereinstimmung mit den Inhalten auf dem Host zu sorgen.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Nächste Schritte

[Erkennen von Bewegung und Ausgeben von Ereignissen](detect-motion-emit-events-quickstart.md)
