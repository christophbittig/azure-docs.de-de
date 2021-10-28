---
title: Bereitstellen in einem IoT Edge für Linux unter Windows mit Live Video Analytics – Azure
description: Dieser Artikel enthält eine Anleitung für die Bereitstellung für IoT Edge für Linux auf einem Windows-Gerät mit Live Video Analytics.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 315501540e38340285e113dace2b68a46c3f6493
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225716"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device-with-live-video-analytics"></a>Bereitstellen in einem EFLOW-Gerät (IoT Edge für Linux unter Windows) mit Live Video Analytics

[!INCLUDE [redirect to Azure Video Analyzer](./includes/redirect-video-analyzer.md)]

In diesem Artikel wird beschrieben, wie Sie Live Video Analytics auf einem Edgegerät bereitstellen, das über [IoT Edge für Linux unter Windows (EFLOW)](../../iot-edge/iot-edge-for-linux-on-windows.md) verfügt. Nachdem Sie die Schritte in diesem Dokument befolgt haben, können Sie einen [Mediengraphen](media-graph-concept.md) ausführen, mit dem Bewegung in einem Video erkannt wird und die entsprechenden Ereignisse an den IoT-Hub in der Cloud ausgegeben werden. Anschließend können Sie den Mediengraphen für erweiterte Szenarien einsetzen und die Leistungsstärke von Live Video Analytics für Ihr Windows-basiertes IoT Edge-Gerät nutzen.

## <a name="prerequisites"></a>Voraussetzungen 

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

    > [!NOTE]
    > Sie benötigen ein Azure-Abonnement mit Berechtigungen zum Erstellen von Dienstprinzipalen. (Die **Rolle „Besitzer“** stellt diese Berechtigungen bereit.) Wenn Sie nicht über die richtigen Berechtigungen verfügen, sollten Sie Ihren Kontoadministrator bitten, Ihnen die richtigen Berechtigungen zu erteilen.
* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Weitere Informationen finden Sie unter [Was ist Azure IoT Edge für Linux unter Windows (Vorschau)?](../../iot-edge/iot-edge-for-linux-on-windows.md).

## <a name="deployment-steps"></a>Bereitstellungsschritte

Hier ist der Inhalt und Ablauf des Dokuments dargestellt. Sie können fünf einfache Schritte ausführen, um Live Video Analytics auf einem Windows-Gerät mit EFLOW nutzen zu können:

:::image type="content" source="./media/deploy-iot-edge-linux-on-windows/eflow.png" alt-text="Diagramm: IoT Edge für Linux unter Windows (EFLOW)":::

1. [Installieren Sie EFLOW](../../iot-edge/how-to-provision-single-device-linux-on-windows-symmetric.md) auf Ihrem Windows-Gerät. 

    1. Falls Sie Ihren Windows-PC verwenden, wird auf der Startseite von [Windows Admin Center](/windows-server/manage/windows-admin-center/overview) unter der Liste mit den Verbindungen eine Verbindung mit dem lokalen Host angezeigt. Diese stellt den PC dar, auf dem Sie Windows Admin Center ausführen. 
    1. Alle weiteren von Ihnen verwalteten Server, PCs oder Cluster werden hier ebenfalls aufgeführt.
    1. Sie können Windows Admin Center verwenden, um Azure EFLOW entweder auf Ihrem lokalen Gerät oder auf verwalteten Remotegeräten zu installieren und zu verwalten. In diesem Leitfaden fungiert die Verbindung mit dem lokalen Host als Zielgerät für die Bereitstellung von Azure IoT Edge für Linux unter Windows. Aus diesem Grund ist „Localhost“ auch als IoT Edge-Gerät aufgeführt.

    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/windows-admin-center.png" alt-text="Bereitstellungsschritte: Windows Admin Center":::
1. Klicken Sie auf das IoT Edge-Gerät, um eine Verbindung damit herzustellen. Die Registerkarten „Übersicht“ und „Befehlsshell“ sollten angezeigt werden. Auf der Registerkarte „Befehlsshell“ können Sie Befehle für Ihr Edgegerät ausführen.
 
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager.png" alt-text="Bereitstellungsschritte: Azure IoT Edge-Manager":::
1. Navigieren Sie zur Befehlsshell, und geben Sie den folgenden Befehl ein:
    
    ```bash
    bash -c "$(curl -sL https://aka.ms/lva-edge/prep_device)"
    ```

    Das Live Video Analytics-Modul wird auf dem Edgegerät mit [lokalen Benutzerkonten](deploy-iot-edge-device.md#create-and-use-local-user-account-for-deployment) ohne Berechtigungen ausgeführt. Darüber hinaus werden [bestimmte lokale Ordner benötigt](deploy-iot-edge-device.md#granting-permissions-to-device-storage), um die Daten für die Anwendungskonfiguration zu speichern. Abschließend nutzen wir für diese Schrittanleitung einen [RTSP-Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555), mit dem ein Videofeed in Echtzeit zur Analyse an ein LVA-Modul weitergeleitet wird. Für diesen Simulator werden als Eingabe vorab aufgezeichnete Videodateien aus einem Eingabeverzeichnis verwendet. 
    
    Mit dem obigen Skript zum Vorbereiten des Geräts werden die Aufgaben automatisiert, damit Sie nur einen Befehl ausführen müssen, um nahtlos alle relevanten Eingabe- und Konfigurationsordner, Videoeingabedateien und Benutzerkonten mit Berechtigungen zu erstellen. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollten auf Ihrem Edgegerät die folgenden Ordner erstellt worden sein. 
    
    * `/home/lvaedgeuser/samples`
    * `/home/lvaedgeuser/samples/input`
    * `/var/lib/azuremediaservices`
    * `/var/media`
    
    Achten Sie auf die Videodateien (*.mkv) im Ordner „/home/lvaedgeuser/samples/input“, die als Eingabedateien für die Analyse dienen. 
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-iot-edge-manager-analysis.png" alt-text="Analyse":::
1. Nachdem Sie nun das Edgegerät eingerichtet und die Registrierung beim Hub durchgeführt haben und die Ausführung mit den richtigen erstellten Ordnerstrukturen erfolgreich ist, ist der nächste Schritt die Einrichtung der folgenden zusätzlichen Azure-Ressourcen und die Bereitstellung des LVA-Moduls. 

    * Speicherkonto
    * Azure Media Services-Konto

    Für diese Aufgabe empfehlen wir Ihnen die Verwendung des [Setupskripts für Live Video Analytics-Ressourcen](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) zum Bereitstellen der erforderlichen Ressourcen in Ihrem Azure-Abonnement. Gehen Sie dazu folgendermaßen vor:

    1. Öffnen Sie [Azure Cloud Shell](https://ms.portal.azure.com/#cloudshell/).

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/azure-resources-cloud-shell.png" alt-text="Cloud Shell":::
    1. Wenn Sie Cloud Shell zum ersten Mal verwenden, werden Sie aufgefordert, ein Abonnement auszuwählen, um ein Speicherkonto und eine Microsoft Azure Files-Freigabe zu erstellen. Wählen Sie **Speicher erstellen** aus, um ein Speicherkonto für die Cloud Shell-Sitzungsinformationen zu erstellen. Dieses Speicherkonto ist von dem Konto getrennt, das vom Skript für die Verwendung mit Ihrem Azure Media Services-Konto erstellt wird.
    1. Wählen Sie auf der linken Seite des Cloud Shell-Fensters im Dropdownmenü Bash als Option für die Umgebung aus.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/bash.png" alt-text="Bash-Umgebung":::
    1. Führen Sie den folgenden Befehl aus.

        ```bash
        bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
        ```
        
        Wählen Sie in der Eingabeaufforderung zur Auswahl Ihres eigenen Edgegeräts als IoT Edge-Gerät die Option **Ja** aus, da Sie zuvor sowohl das Gerät als auch den IoT-Hub erstellt haben. Sie werden auch aufgefordert, den Namen Ihres IoT-Hubs und die ID des IoT Edge-Geräts einzugeben. Sie können auf diese Angaben zugreifen, indem Sie sich beim Azure-Portal anmelden, auf Ihren IoT-Hub klicken und dann auf dem Blatt des IoT-Hub-Portals auf die IoT Edge-Option zugreifen.

        :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/iot-edge-devices.png" alt-text="IoT Edge-Geräte":::

    Nach Abschluss des Vorgangs können Sie sich wieder bei der Befehlsshell des IoT Edge-Geräts anmelden und den folgenden Befehl ausführen.
    
    `sudo iotedge list`
    
    Auf Ihrem Edgegerät sollten die folgenden vier Module bereitgestellt und ausgeführt werden. Beachten Sie Folgendes: Mit dem Skript für die Ressourcenerstellung wird das LVA-Modul zusammen mit den IoT Edge-Modulen („edgeAgent“ und „edgeHub“) und einem RTSP-Simulatormodul bereitgestellt, um die Simulation des RTSP-Videofeeds zu ermöglichen.
    
    :::image type="content" source="./media/deploy-iot-edge-linux-on-windows/running.png" alt-text="Status":::
1. Nachdem die Module bereitgestellt und eingerichtet wurden, können Sie Ihren ersten LVA-Mediengraphen unter EFLOW ausführen. Sie können wie unten angegeben einen einfachen Graphen für die Bewegungserkennung ausführen und die Ergebnisse visualisieren, indem Sie die folgenden Schritte ausführen:

    :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Graph für Bewegungserkennung":::

    1. [Konfigurieren](get-started-detect-motion-emit-events-quickstart.md#configure-the-azure-iot-tools-extension) Sie die Azure IoT Tools-Erweiterung.
    
        > [!Note]
        > Verwenden Sie den folgenden Pfad: `~/clouddrive/lva_byod/appsettings.json. - instead of ~/clouddrive/lva-sample/appsettings.json`.
    1. Legen Sie die Topologie fest, instanziieren Sie einen Graphen, und aktivieren Sie ihn über diese [direkten Methodenaufrufe](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls).
    1. [Verfolgen Sie die Ergebnisse](get-started-detect-motion-emit-events-quickstart.md#observe-results) auf dem Hub.
    1. Rufen Sie die [Bereinigungsmethoden](get-started-detect-motion-emit-events-quickstart.md#invoke-graphinstancedeactivate) auf.
    1. Löschen Sie Ihre Ressourcen, falls Sie diese nicht mehr benötigen.

        > [!IMPORTANT]
        > Nicht gelöschte Ressourcen können weiterhin aktiv sein und Azure-Kosten verursachen.
    
## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie die Bewegungserkennung mit der Aufzeichnung relevanter Videos in der Cloud aus. Führen Sie die Schritte aus, die in der Schnellstartanleitung zum [Erkennen von Bewegung und Aufzeichnen von Video in Media Services](detect-motion-record-video-clips-media-services-quickstart.md#review-the-sample-video) angegeben sind.
* Führen Sie [KI für Livevideos](use-your-model-quickstart.md#overview) aus. (Sie können die Erfüllung der Voraussetzungen überspringen, da Sie dies oben bereits durchgeführt haben.)
* Verwenden Sie unsere [VS Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge), um weitere Mediengraphen anzuzeigen.
* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
