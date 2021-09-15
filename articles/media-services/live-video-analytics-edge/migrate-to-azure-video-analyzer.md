---
title: Migrieren von Live Video Analytics zu Azure Video Analyzer
description: Erfahren Sie, wie Sie die Migration von Live Video Analytics zu Azure Video Analyzer durchführen.
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: 452b3430dd1e0eafbfe35166339e42bcbfe1f47b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123471694"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>Migrieren von Live Video Analytics zu Azure Video Analyzer

Dieser Artikel sollte verwendet werden, wenn Sie das Live Video Analytics-Edge-Modul auf einem IoT Edge-Gerät bereitgestellt haben und derzeit Mediengraphen ausführen, um Feeds von RTSP-Kameras (Real Time Streaming Protocol) zu verarbeiten.

Wenn Sie keine RTSP-Kameras verwenden und [einen Schnellstart oder ein Tutorial zu Live Video Analytics](../live-video-analytics-edge/overview.md) nutzen, verwenden Sie stattdessen einfach den bzw. das entsprechende(n) [Schnellstart oder Tutorial für Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/overview.md).

Die folgenden Anweisungen gelten für die Migration von Live Video Analytics zu Azure Video Analyzer für ein einzelnes IoT Edge-Gerät. Sie müssen die Schritte für jedes einzelne IoT Edge-Gerät wiederholen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Abonnement.

   > [!NOTE]
   > Sie benötigen ein Azure-Abonnement, in dem Sie Zugriff auf die Rolle  [Mitwirkender](../../role-based-access-control/built-in-roles.md)  und die Rolle  [Benutzerzugriffsadministrator](../../role-based-access-control/built-in-roles.md)  für die Ressourcengruppe haben, unter der Sie neue Ressourcen erstellen (vom Benutzer zugewiesene verwaltete Identität, Speicherkonto, Video Analyzer-Konto). Wenn Sie nicht über die richtigen Berechtigungen verfügen, wenden Sie sich an Ihren Kontoadministrator, damit er Ihnen diese Berechtigungen erteilt. 

* Sie benötigen Administratorrechte für die IoT Edge Geräte, auf denen Live Video Analytics installiert ist.

## <a name="create-a-backup-of-current-settings"></a>Erstellen einer Sicherung der aktuellen Einstellungen

Es wird dringend empfohlen, Ihr Edge-Bereitstellungsmanifest und Ihre Live Video Analytics-Mediengraphkonfigurationen zu speichern, bevor Sie mit der Migration beginnen.

> [!NOTE]
> Die unter den RTSP-Anmeldeinformationen Ihrer Kamera aufgeführten Schritte und die Stream-URL-Details können nicht abgerufen werden.  Sie müssen diese Informationen direkt aus der Verwaltungsanwendung der Kamera abrufen.

### <a name="save-the-current-deployment-manifest"></a>Speichern des aktuellen Bereitstellungsmanifests

1. Melden Sie sich beim Azure-Portal an.

1. Wechseln Sie zu **IoT Hub**, und wählen Sie unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Wählen Sie unter **IoT Edge** das IoT Edge-Gerät aus, das Sie zu Azure Video Analyzer migrieren möchten.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="Screenshot: IoT Edge-Geräte":::

1. Wählen Sie **Module festlegen** und anschließend **Überprüfen + erstellen** aus.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="Screenshot des Bereitststellungsmanifests":::

1. Kopieren Sie im Fenster den Abschnitt **Bereitstellung**, und speichern Sie die Kopie an einem sicheren Ort.

1. Klicken Sie oben rechts im Portal auf **X**.  

    > [!NOTE]
    > *Klicken Sie nicht* auf **Erstellen**.  Überprüfen Sie das Bereitstellungsmanifest, und suchen Sie den Knoten `lvaEdge` unter `modules`.  Wenn unter dem Knoten `lvaEdge` der Knoten `env` vorhanden ist, notieren Sie sich die Werte von LOCAL_EDGE_USER und lOCAL_GROUP_ID, die Sie weiter unten in diesem Dokument benötigen.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="Screenshot: Bereitstellungsmanifest und den Abschnitt „env“ für den lokalen Edge-Benutzer und die lokale Edge-Gruppe":::

### <a name="save-the-live-video-analytics-topologies"></a>Speichern der Live Video Analytics-Topologien

1. Wählen Sie zum Speichern des Live Video Analytics-Mediengraphen das Live Video Analytics-Modul unter dem IoT Edge-Gerät aus.  Im folgenden Beispiel lautet der Containername *lvaEdge*.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Screenshot: Containername des Live Video Analytics-IoT Edge-Moduls":::

1. Wählen Sie **Direkte Methode** aus.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Screenshot: Aufruf von „Direkte Methode“ für Live Video Analytics":::

1. Geben Sie im Feld „Methodenname“ **GraphTopologyList** und in „Nutzlast“ den folgenden Code ein:

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="Screenshot: Aufruf von „Direkte Methode“ für IoT Edge":::

1. Wählen Sie **Methode aufrufen** aus.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="Screenshot von „Methode aufrufen“":::

1. Die Rückgabe wird im Abschnitt „Ergebnis“ angezeigt.  Dies ist Ihre Live Video Analytics-Graphtopologie.  Kopieren Sie den JSON-Code, und speichern Sie ihn an einem sicheren Ort.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="Screenshot: Ergebnis der GraphTopologyList-Methode":::

1. Wiederholen Sie diesen Vorgang, ersetzen Sie jedoch `GraphTopologyList` durch `GraphInstanceList`, und speichern Sie den Abschnitt „Ergebnis“.  Dies ist eine Liste Ihrer Graphinstanzen. Notieren Sie sich die Namen aller aktiven Instanzen.

1.  Nachdem Sie eine Sicherungskopie Ihrer Live Video Analytics-Einstellungen erstellt haben, können Sie *alle aktiven Mediengraphen deaktivieren*, indem Sie in **Methodenname** den Text **GraphInstanceDeactivate** eingeben und dann den folgenden Code eingeben:

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Ersetzen Sie Graph_Name durch den Namen der aktiven Instanzen in den neuen Ergebnissen.  Wiederholen Sie diesen Vorgang, bis alle aktiven Graphen deaktiviert sind. 

## <a name="azure-video-analyzer-device-preparation"></a>Azure Video Analyzer: Gerätevorbereitung 

Zum Starten der Migration muss das IoT Edge-Gerät für die Ausführung des Azure Video Analyzer-Moduls konfiguriert werden. Das Azure Video Analyzer-Modul muss auf dem IoT Edge-Gerät mit einem lokalen Benutzerkonto ohne Berechtigungen ausgeführt werden. Für das Modul werden bestimmte lokale Ordner benötigt, um die Daten für die Anwendungskonfiguration zu speichern. Für das RTSP-Kamerasimulatormodul werden Videodateien benötigt, mit denen ein Livevideofeed synthetisiert werden kann. 

1. Melden Sie sich per SSH beim IoT Edge-Gerät an.

    > [!Note]
    > Basierend auf der Überprüfung Ihrer IoT Edge-Gerätemanifestdatei (Schritt 6 im Abschnitt „Erstellen einer Sicherung der aktuellen Einstellungen“) müssen Sie entweder einen neuen Benutzer bzw. eine neue Gruppe auf dem IoT Edge-Gerät erstellen oder die in Live Video Analytics erstellten Benutzer/Gruppen wiederverwenden.

    Wenn das IoT Edge-Gerät mit einer lokalen Benutzer-ID und Gruppen-ID für Live Video Analytics (im Bereitstellungsmanifest) konfiguriert wurde, können Sie Schritt 1 überspringen. Notieren Sie sich jedoch den Benutzer- und Gruppennamen und den Wert. Sie werden weiter unten in diesem Dokument verwendet.  Wenn Sie zudem eine eindeutige Ordnerstruktur für Ihre Live Video Analytics-Bereitstellung verwenden (im Bereitstellungsmanifest), ersetzen Sie *videoanalyzer* durch diese Werte, und lassen Sie die ersten beiden Zeilen in Schritt 2 weg.  

    Das folgende Beispiel zeigt eine eindeutige Ordnerstruktur für eine Live Video Analytics-Bereitstellung.  Beachten Sie, dass das *applicationDataDirectory* „/var/lib/videofiles“ lautet. In diesem Beispiel wurden von Live Video Analytics auf dem IoT Edge-Gerät *localedgeuser* und *localedgegroup* erstellt.

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    Führen Sie dann den folgenden Code aus:

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. Erstellen Sie ein lokales Benutzerkonto auf dem IoT Edge-Gerät, indem Sie den folgenden Code ausführen:

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. Erstellen Sie mit dem folgenden Code die *avaedge*-Ordnerstruktur :

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

Im nächsten Schritt erstellen Sie die erforderlichen Azure-Ressourcen. Die folgenden Anweisungen sind im Abschnitt [Erstellen von Azure-Ressourcen](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md) dokumentiert und werden hier wiedergegeben. 

Sie erstellen jetzt die erforderlichen Azure-Ressourcen (Video Analyzer-Konto, Speicherkonto, benutzerseitig zugewiesene verwaltete Identität). 

Wenn Sie ein Azure Video Analyzer-Konto erstellen, müssen Sie es ein Azure-Speicherkonto zuordnen. Wenn Sie Video Analyzer verwenden, um das Live-Video von einer Kamera aufzuzeichnen, werden diese Daten als Blobs in einem Container im Speicherkonto gespeichert. Sie müssen eine verwaltete Identität verwenden, um dem Video Analyzer-Konto den entsprechenden Zugriff auf das Speicherkonto zu gewähren. 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Erstellen eines Video Analyzer-Kontos im Azure-Portal

1. Melden Sie sich beim  [Azure-Portal](https://portal.azure.com/) an. 

1. Geben Sie im Suchfeld  **Video Analyzer** ein. 

1. Wählen Sie unter  **Dienste** den Eintrag **Video Analyzer** aus. 

1. Wählen Sie **Hinzufügen**. 

1. Geben Sie im Abschnitt **Create Video Analyzer account** (Video Analyzer-Konto erstellen) die folgenden erforderlichen Werte ein: 

   - **Abonnement**: Wählen Sie das Abonnement aus, das Sie zum Erstellen des Video Analyzer-Kontos verwenden möchten. 

   - **Ressourcengruppe**: Wählen Sie eine Ressourcengruppe aus, in der Sie das Video Analyzer-Konto erstellen möchten, oder wählen Sie  **Neu erstellen** aus, um eine Ressourcengruppe zu erstellen. 

   - **Video Analyzer account name** (Name des Video Analyzer-Kontos): Geben Sie einen Namen für Ihr Video Analyzer-Konto ein. Der Name darf nur aus drei bis 24 Kleinbuchstaben oder Zahlen bestehen und keine Leerzeichen enthalten. 

   - **Standort**: Wählen Sie einen Standort zum Bereitstellen Ihres Video Analyzer-Kontos aus (beispielsweise  **USA, Westen 2**). 

   - **Speicherkonto**: Erstellen Sie ein Speicherkonto. Es wird empfohlen, ein [universelles Standardspeicherkonto der Version 2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) auszuwählen. 

      > [!NOTE]
      > Wenn Sie über ein Speicherkonto in der Region verfügen, in der Sie das Azure Video Analyzer-Konto bereitstellen, können Sie dieses Speicherkonto verwenden, anstatt ein neues zu erstellen. 

   - **Benutzeridentität:** Erstellen Sie eine neue benutzerseitig zugewiesene verwaltete Identität, und geben Sie ihr einen Namen. 

1. **Aktivieren** Sie unten im Formular das Kästchen neben dem Text: „Ich bestätige, dass ich alle erforderlichen Rechte und Einwilligungen nach geltendem Recht eingeholt habe, um Medieninhalte zu verarbeiten. Ich erkenne alle geltenden Produktbedingungen als für mich verbindlich an.“ 

1. Wählen Sie am unteren Rand des Formulars die Option **Überprüfen + erstellen** aus. 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Screenshot: Erstellen des Video Analyzer-Kontos im Azure-Portal":::

   > [!NOTE]
   > Ihrer Ressourcengruppe werden diese Azure-Ressourcen hinzugefügt.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Screenshot: Ressourcen, die Ihrer Azure-Ressourcengruppe ebenfalls hinzugefügt werden":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>Bereitstellen des Azure Video Analyzer-Edgemoduls

### <a name="generate-azure-video-analyzer-edge-module-token"></a>Generieren des Azure Video Analyzer-Edgemodultokens

1. Navigieren Sie zu Ihrem Video Analyzer-Konto.

1. Wählen Sie im Bereich **Edge** die Option **Edgemodule** aus.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Screenshot: Erstellung des Edgemoduls für das Azure Video Analyzer-Konto":::

1. Wählen Sie **Add edge modules** (Edgemodule hinzufügen) aus, geben Sie **avaedge** als Name des neuen Edgemoduls ein, und wählen Sie anschließend **Hinzufügen** aus.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="Screenshot: Hinzufügen von Edgemodulen":::

1. Auf der rechten Seite wird der Bereich **Kopieren Sie das Bereitstellungstoken** angezeigt. Kopieren Sie den Codeausschnitt unter **Recommended desired properties for IoT module deployment** (Empfohlene gewünschte Eigenschaften für die IoT-Modulbereitstellung).  Er wird in einem späteren Schritt benötigt.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="Screenshot des Bereichs „Kopieren Sie das Bereitstellungstoken“":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>Bereitstellen des Azure Video Analyzer-Edgemoduls

1.  Navigieren Sie zu Azure IoT Hub.

1. Wählen Sie unter **Automatische Geräteverwaltung** die Option **IoT Edge** aus.

1. Wählen Sie unter „Geräte-ID“ den Wert für Ihr IoT Edge-Gerät aus.

1. Wählen Sie Module festlegen aus.

1. Wählen Sie **Hinzufügen** und anschließend im Dropdownmenü die Option **IoT Edge-Modul aus Marketplace** aus.

1. Geben Sie im Suchfeld „Azure Video Analyzer“ ein.

1. Wählen Sie „Azure Video Analyzer“ aus.

1. Wählen Sie im Bereich **Module festlegen** die Option **AzureVideoAnalyzerEdge** aus.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="Screenshot: Hinzufügen von Azure Video Analyzer als IoT Edge-Modul":::

1. Geben Sie im Feld „Name des IoT Edge-Moduls“ **avaedge** ein.
1. Wählen Sie **Umgebungsvariablen** aus. 

   > [!NOTE]
   > Wenn in Ihrer Live Video Analytics Bereitstellung in Schritt 6 im Abschnitt „Erstellen einer Sicherung der aktuellen Einstellungen“ ein lokaler Benutzer und eine lokale Gruppe verwendet wurden, verwenden Sie im folgenden Schritt diese Werte anstelle von LOCAL_USER_ID und LOCAL_GROUP_ID.

1. Geben Sie im Feld „Name“ **LOCAL_USER_ID** und im Feld „Wert“ **1010** ein.  Geben Sie im nächsten Feld „Name“ **LOCAL_GROUP_ID** und im Feld „Wert“ **1010** ein.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Screenshot: Umgebungsvariablen von Azure Video Analyzer":::

1. Wählen Sie **Optionen für Containererstellung** aus, und fügen Sie Folgendes ein:

      ```json
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
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1. Wählen Sie **Einstellungen für Modulzwilling** aus, und fügen Sie den Codeausschnitt ein, den Sie zuvor im Video Analyzer-Konto auf der Seite **Kopieren Sie das Bereitstellungstoken** kopiert haben. 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` ist ein Platzhalter für das Bereitstellungstoken, das weiter oben im Abschnitt „Generieren des Azure Video Analyzer-Edgemodultokens“ in Schritt 4 erfasst wurde.

1. Wählen Sie **Aktualisieren** aus.

1. Wählen Sie **Routen** aus.
1. Geben Sie unter **NAME** den Text **AVAToHub** ein. Geben Sie unter **WERT** den Text __FROM /messages/modules/avaedge/outputs/* INTO $upstream__ ein. 
1. Entfernen Sie die Route **LVAToHub**, indem Sie das Papierkorbsymbol rechts neben der Route auswählen.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Screenshot: Löschen der Live Video Analytics-IoT Edge-Route":::

1. Suchen Sie unter **Module** das Modul *lvaEdge*, und entfernen Sie es, indem Sie auf der rechten Seite das Papierkorbsymbol auswählen.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Screenshot: Löschen der Live Video Analytics-IoT Edge-Moduls":::

1. Wählen Sie **Überprüfen + erstellen** und dann  **Erstellen** aus, um das Edgemodul **avaedge** bereitzustellen. 

### <a name="verify-your-deployment"></a>Überprüfen Ihrer Bereitstellung

Vergewissern Sie sich auf der Seite „Gerätedetails“, dass das Modul **avaedge**  als **In Bereitstellung angegeben** und als **Vom Gerät gemeldet** aufgeführt ist. 

Unter Umständen dauert es einen Moment, bis das Modul auf dem Gerät gestartet und an IoT Hub gemeldet wurde. Aktualisieren Sie die Seite, um einen aktualisierten Status anzuzeigen. Der Statuscode **200 -- OK** bedeutet, dass die [IoT Edge-Runtime](../../iot-edge/iot-edge-runtime.md) fehlerfrei ist und ordnungsgemäß funktioniert. 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="Screenshot: Für die IoT Edge-Runtime wird der Status 200 gemeldet.":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="Screenshot: Für die IoT Edge Runtime wird gemeldet, dass alle Module ausgeführt werden.":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>Konvertieren von Mediengraphtopologien in Azure Video Analyzer-Pipelines

Es gibt einige Unterschiede zwischen den von Live Video Analytics verwendeten Mediengraphtopologien und den von Azure Video Analyzer verwendeten Pipelinetopologien.  Verwenden Sie die folgende Tabelle, um die Live Video Analytics-Topologien und die Azure Video Analyzer-Topologien zu vergleichen.

| Szenario                                                     | Mediengraph (Live Video Analytics)                                            | Pipeline (Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Aufzeichnen eines relevanten Objekts mit mehreren KI-Modellen         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| Video und Audio                                              | [audio-video]()                                               | [audio-video]()                                               |
| Fortlaufende Videoaufzeichnung                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| Fortlaufende Videoaufzeichnung und Rückschluss mithilfe der gRPC-Erweiterung | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| Fortlaufende Videoaufzeichnung und Rückschluss mithilfe der HTTP-Erweiterung | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| Kontinuierliche Videoaufzeichnung mit Bewegungserkennung             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| Ereignisbasierte Videoaufzeichnung in [Azure Video Analyzer-Video/Medienobjekten] basierend auf Ereignissen aus externer KI | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| Ereignisbasierte Videoaufzeichnung in [Azure Video Analyzer-Video/Medienobjekten] basierend auf Ereignissen aus externer KI | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| Ereignisbasierte Videoaufzeichnung in [Medienobjekten/Videosenke] basierend auf bestimmten Objekten, die von einer externen Rückschluss-Engine erkannt werden | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| Ereignisbasierte Aufzeichnung von Video in Dateien auf Grundlage von Nachrichten, die über IoT Edge Hub gesendet werden | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| Ereignisbasierte Videoaufzeichnung in lokalen Dateien basierend auf Bewegungsereignissen | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| Ereignisbasierte Videoaufzeichnung in [Medienobjekten/Videosenke] und lokalen Dateien basierend auf Bewegungsereignissen | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| Analysieren von Livevideos mithilfe der gRPC-Erweiterung zum Senden von Bildern an das Edge-KI-Erweiterungsmodul OpenVINO(TM) DL Streamer von Intel | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| Analysieren von Livevideos mithilfe der HTTP-Erweiterung zum Senden von Bildern an eine externe Rückschluss-Engine | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| Analysieren von Livevideos mithilfe der HTTP-Erweiterung zum Senden von Bildern an das KI-Erweiterungsmodul OpenVINO™ Model Server von Intel | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| Analysieren von Livevideos mit maschinellem Sehen für die räumliche Analyse | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> In unserem Abschnitt mit Beispielen auf GitHub finden Sie [Live Video Analytics-Mediengraphtopologien](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies) und [Azure Video Analyzer-Pipelinetopologien](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies) und können sie vergleichen. Das Überprüfen dieser Beispiele ermöglicht es Ihnen, Ihre vorhandenen Mediengraphtopologien auf Azure Video Analyzer-Pipelinetopologien zu aktualisieren.

### <a name="set-and-activate-the-pipeline-topology"></a>Festlegen und Aktivieren der Pipelinetopologie

Nachdem Sie die Mediengraphtopologie (Live Video Analytics) in die Pipelinetopologie (Azure Video Analyzer) konvertiert haben, müssen Sie die Pipeline im Modul *avaedge* festlegen.

1. Um die Topologie festzulegen, wählen Sie unter dem IoT Edge-Gerät das Azure Video Analyzer-Edgemodul aus.  Im folgenden Beispiel lautet der Containername *avaedge*.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Screenshot: das Live Video Analytics-IoT Edge-Modul":::

1. Wählen Sie **Direkte Methode** aus.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Screenshot: Aufruf von „Direkte Methode“ für Live Video Analytics":::

1. Geben Sie in **Methodenname** den Namen **pipelineTopologySet** ein, und geben Sie im Feld „Nutzlast“ den JSON-Code der konvertierten Pipeline ein.

1. Wählen Sie **Methode aufrufen** aus.

      Im Rückgabefeld sollte „"status" : 201“, gefolgt von der Topologie angezeigt werden.  Dieser Status gibt an, dass eine neue Topologie erstellt wurde.

1. Als Nächstes müssen wir mithilfe der Topologie die Livepipeline erstellen.  Führen Sie die obigen Schritte 1–3 aus, geben Sie jedoch im Feld „Methodenname“ **livePipelineSet** und im Feld „Nutzlast“ Folgendes ein:

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > Ersetzen Sie die Werte `<live pipeline name>`, `<Name of the set topology>`, `<RTSP camera string>` und `<Camera User Password>` durch die Werte für Ihre Umgebung. 

1. Wählen Sie **Methode aufrufen** aus.
   
      Im Rückgabefeld sollte „"status" : 201“, gefolgt von der Livepipeline angezeigt werden.  Dieser Status gibt an, dass eine neue Pipeline erstellt wurde.

2. Aktivieren Sie die Livepipeline, indem Sie die Schritte 1–3 ausführen. Geben Sie jedoch in **Methodenname** den Namen **livePipelineActivate** und im Feld „Nutzlast“ Folgendes ein:

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > Ersetzen Sie `<live pipeline name>` durch den oben festgelegten Namen der Livepipeline.

4. Wählen Sie **Methode aufrufen** aus.
   
   Im Rückgabefeld sollte „"status" : 200“ angezeigt werden.  Dieser Status gibt an, dass eine neue Pipeline erstellt wurde.

## <a name="direct-methods"></a>Direkte Methoden

Sowohl Live Video Analytics als auch Azure Video Analyzer verwenden direkte Methodenaufrufe.  Beachten Sie außerdem, dass bei der Migration die direkten Methoden aktualisiert wurden und eine Überprüfung aller Workflows erforderlich ist, die sie möglicherweise in Live Video Analytics-Lösungen verwenden.  In der folgenden Tabelle werden die Unterschiede zwischen [Aufrufen direkter Live Video Analytics-Methoden](direct-methods.md) und [Aufrufen direkter Azure Video Analyzer-Methoden](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md) dargestellt.

| Livevideoanalyse | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>Verschiedene Änderungen 

- appsettings.json: Aktualisieren Sie die Datei „appsettings.json“, sodass moduleId auf Ihr Modul verweist. Hierzu müssen Sie diese auf *avaeEdge* festlegen. 

- Am [gRPC-Vertrag](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc) wurde eine geringfügige Änderung vorgenommen. Ihre vorhandene gRPC-Erweiterung sollte zwar weiterhin funktionsfähig sein, es empfiehlt sich jedoch, die Prototypdateien zu aktualisieren. Bei der Änderung handelt es sich um eine neue Zeile in „inferencing.proto : string sequence_id=14“.

## <a name="next-steps"></a>Nächste Schritte

- Probieren Sie  [Schnellstart: Erkennen von Bewegung, Aufzeichnen von Video in Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md) aus. 
- Probieren Sie  [Schnellstart: Analysieren von Livevideos mit einem eigenen Modell – HTTP](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md) aus. 
- Machen Sie sich mit den [Azure Video Analyzer-Begriffen](../../azure-video-analyzer/video-analyzer-docs/terminology.md) vertraut. 
- Lernen Sie die [Azure Video Analyzer-Pipeline](../../azure-video-analyzer/video-analyzer-docs/pipeline.md) kennen. 
