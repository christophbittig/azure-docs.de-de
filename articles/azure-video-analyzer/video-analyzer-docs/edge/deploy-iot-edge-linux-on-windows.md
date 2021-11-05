---
title: Bereitstellung auf einem Windows-Gerät mit EFLOW
description: Dieser Artikel enthält eine Anleitung für die Bereitstellung für IoT Edge für Linux auf einem Windows-Gerät.
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3e2e045a72700b7e2c2e96cf63f344a84173b0f7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095442"
---
# <a name="deploy-to-an-iot-edge-for-linux-on-windows-eflow-device"></a>Bereitstellen für IoT Edge für Linux auf einem Windows-Gerät (EFLOW)

[!INCLUDE [header](includes/edge-env.md)]

In diesem Artikel wird beschrieben, wie Sie Azure Video Analyzer auf einem Edgegerät bereitstellen, das über [IoT Edge für Linux unter Windows (EFLOW)](../../../iot-edge/iot-edge-for-linux-on-windows.md) verfügt. Wenn Sie die Schritte in diesem Dokument durchgeführt haben, können Sie eine [Pipeline](../pipeline.md) ausführen, die Bewegungen in einem Video erkennt und die entsprechenden Ereignisse an IoT Hub übermittelt. Anschließend können Sie die Pipeline für erweiterte Szenarien einsetzen und die Leistungsstärke von Azure Video Analyzer für Ihr Windows-basiertes IoT Edge-Gerät nutzen.

## <a name="prerequisites"></a>Voraussetzungen 

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), falls Sie noch keins besitzen.

* Installation von [Visual Studio Code](https://code.visualstudio.com/) auf Ihrem Entwicklungscomputer. Stellen Sie sicher, dass Sie über die [Azure IoT Tools-Erweiterung](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) verfügen.
* Weitere Informationen finden Sie unter [Was ist Azure IoT Edge für Linux unter Windows (Vorschau)?](../../../iot-edge/iot-edge-for-linux-on-windows.md).

## <a name="deployment-steps"></a>Bereitstellungsschritte

Hier ist der Inhalt und Ablauf des Dokuments dargestellt. Sie können fünf einfache Schritte ausführen, um Azure Video Analyzer auf einem Windows-Gerät mit EFLOW nutzen zu können:

![Diagramm von IoT Edge für Linux unter Windows (E FLOW).](./media/deploy-iot-edge-linux-on-windows/eflow.png)

1. [Installieren Sie EFLOW](../../../iot-edge/how-to-install-iot-edge-on-windows.md) mithilfe der PowerShell auf Ihrem Windows-Gerät.

    > [!NOTE]
    > Es gibt zwei Möglichkeiten, EFLOW bereitzustellen (PowerShell und Windows Admin Center), und zwei Möglichkeiten, den virtuellen Computer bereitzustellen (manuelle Bereitstellung mithilfe der Verbindungszeichenfolge und manuelle Bereitstellung mit X.509-Zertifikaten). Befolgen Sie die [PowerShell-Bereitstellung](../../../iot-edge/how-to-install-iot-edge-on-windows.md#create-a-new-deployment), und [stellen Sie den Computer mithilfe der Verbindungszeichenfolge aus IoT Hub bereit](../../../iot-edge/how-to-install-iot-edge-on-windows.md#manual-provisioning-using-the-connection-string).

1. Nachdem EFLOW eingerichtet wurde, geben Sie den Befehl `Connect-EflowVm` in PowerShell ein (mit Administratorrechten), um eine Verbindung herzustellen. Dadurch wird ein Bash-Terminal für die Steuerung des virtuellen EFLOW-Computers in PowerShell angezeigt, in dem Sie Linux-Befehle ausführen können, einschließlich Hilfsprogrammen wie Top und Nano. 

    > [!TIP] 
    > Geben Sie im Terminal `exit` ein, um die EFLOW-VM zu beenden.

1. Melden Sie sich über PowerShell bei der EFLOW-VM an, und geben Sie die folgenden Befehle ein:

    `bash -c "$(curl -sL https://aka.ms/ava-edge/prep_device)"`
    
    `sudo iptables -I INPUT -p udp -j ACCEPT`

    Video Analyzer benötigt bestimmte lokale Ordner, um die Konfigurationsdaten für die Anwendung zu speichern. Für diese Schrittanleitung wird ein [RTSP-Simulator](https://github.com/Azure/video-analyzer/tree/main/edge-modules/sources/rtspsim-live555) verwendet, mit dem ein Videofeed in Echtzeit zur Analyse an das Video Analyzer-Modul weitergeleitet wird. Für diesen Simulator werden als Eingabe vorab aufgezeichnete Videodateien aus einem Eingabeverzeichnis verwendet. 

    Mit dem obigen Skript zum Vorbereiten des Geräts werden die Aufgaben automatisiert, damit Sie nur einen Befehl ausführen müssen, um nahtlos alle relevanten Eingabe- und Konfigurationsordner, Videoeingabedateien und Benutzerkonten mit Berechtigungen zu erstellen. Nachdem der Befehl erfolgreich abgeschlossen wurde, sollten auf Ihrem Edgegerät die folgenden Ordner erstellt worden sein. 

    * `/home/localedgeuser/samples`
    * `/home/localedgeuser/samples/input`
    * `/var/lib/videoanalyzer`
    * `/var/media`

    Achten Sie auf die Videodateien (*.mkv) im Ordner „/home/localedgeuser/samples/input“, die als Eingabedateien für die Analyse dienen. 
    
1. Nachdem Sie nun das Edgegerät eingerichtet und die Registrierung beim Hub durchgeführt haben und die Ausführung mit den richtigen erstellten Ordnerstrukturen erfolgreich abgeschlossen wird, ist der nächste Schritt die Einrichtung der folgenden zusätzlichen Azure-Ressourcen und die Bereitstellung des Video Analyzer-Moduls. Die folgende Bereitstellungsvorlage kümmert sich um die Ressourcenerstellung:

    [![In Azure bereitstellen](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)
    
    Der Bereitstellungsprozess dauert ungefähr 20 Minuten. Nach Abschluss haben Sie bestimmte Azure-Ressourcen im Azure-Abonnement bereitgestellt. Hierzu zählen unter anderem:

    * Video Analyzer-Konto: Dieser Clouddienst wird zum Registrieren des Video Analyzer-Edgemoduls und zur Wiedergabe aufgezeichneter Videos und Videoanalysen verwendet.
    * Speicherkonto: Zum Speichern aufgezeichneter Videos und Videoanalysen.
    * Verwaltete Identität: Dies ist die benutzerseitig zugewiesene verwaltete Identität, die zum Verwalten des Zugriffs auf das obige Speicherkonto verwendet wird.
    * IoT Hub: Dient als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung, IoT Edge-Modulen und den verwalteten Geräten.

    Wenn Sie bei der Vorlage gefragt werden, ob Sie ein Edgegerät benötigen, wählen Sie die Option „Vorhandenes Edgegerät verwenden“ aus, da Sie zuvor sowohl das Gerät als auch den IoT Hub erstellt haben. Außerdem werden Sie in den folgenden Schritten aufgefordert, den Namen Ihres IoT Hub und die ID des IoT Edge-Geräts einzugeben.  
    
    ![Vorhandenes Gerät verwenden](./media/deploy-iot-edge-linux-on-windows/use-existing-device.png) 

    Nach Abschluss des Vorgangs können Sie sich wieder bei der EFLOW-VM anmelden und den folgenden Befehl ausführen.

    **`sudo iotedge list`**

    Auf Ihrem Edgegerät sollten die folgenden vier Module bereitgestellt und ausgeführt werden. Beachten Sie Folgendes: Mit dem Skript für die Ressourcenerstellung wird das AVA-Modul zusammen mit den IoT Edge-Modulen („edgeAgent“ und „edgeHub“) und einem RTSP-Simulatormodul bereitgestellt, um die Simulation des RTSP-Videofeeds zu ermöglichen.
    
    ![Bereitgestellte Module](./media/vscode-common-screenshots/avaedge-module.png)
    
1. Nachdem die Module bereitgestellt und eingerichtet wurden, können Sie Ihre erste AVA-Pipeline unter EFLOW ausführen. Sie können wie unten angegeben eine einfache Pipeline für die Bewegungserkennung ausführen und die Ergebnisse visualisieren, indem Sie die folgenden Schritte ausführen:

    ![Auf Bewegungserkennung basierter Video Analyzer](./media/get-started-detect-motion-emit-events/motion-detection.svg)

    1. [Konfigurieren](get-started-detect-motion-emit-events.md#prepare-to-monitor-the-modules) Sie die Azure IoT Tools-Erweiterung.
    1. Legen Sie die pipelineTopology fest, instanziieren Sie eine livePipeline, und aktivieren Sie diese über diese [direkten Methodenaufrufe](get-started-detect-motion-emit-events.md#use-direct-method-calls).
    1. [Verfolgen Sie die Ergebnisse](get-started-detect-motion-emit-events.md#observe-results) auf dem Hub.
    1. Rufen Sie die [Bereinigungsmethoden](get-started-detect-motion-emit-events.md#deactivate-the-live-pipeline) auf.
    1. Löschen Sie Ihre Ressourcen, falls Sie diese nicht mehr benötigen.

        > [!IMPORTANT]
        > Nicht gelöschte Ressourcen können weiterhin aktiv sein und Azure-Kosten verursachen. Stellen Sie sicher, dass Sie die Ressourcen löschen, die Sie nicht verwenden möchten.
   
## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie die Bewegungserkennung mit der Aufzeichnung relevanter Videos in der Cloud aus. Führen Sie die Schritte im Schnellstart [Erkennen von Bewegung und Aufzeichnen von Videoclips](detect-motion-record-video-edge-devices.md) aus.
* Verwenden Sie unsere [VS Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.live-video-analytics-edge), um weitere Pipelines anzuzeigen.
* Verwenden Sie eine [IP-Kamera](https://en.wikipedia.org/wiki/IP_camera) mit RTSP-Unterstützung anstelle des RTSP-Simulators. IP-Kameras, die RTSP unterstützen, finden Sie auf der [Seite mit den ONVIF-konformen Produkten](https://www.onvif.org/conformant-products/). Suchen Sie nach Geräten, die mit den Profilen G, S oder T konform sind.
* Führen Sie [KI für Livevideos](analyze-live-video-use-your-model-http.md#overview) aus. (Sie können die Erfüllung der Voraussetzungen überspringen, da Sie dies oben bereits durchgeführt haben.)

    > [!WARNING] 
    > Für fortgeschrittene Benutzer, die speicherintensive KI-Modelle wie YOLO ausführen möchten, müssen Sie möglicherweise die Ressourcen erhöhen, die der EFLOW-VM zugewiesen sind. Beenden Sie zunächst den virtuellen EFLOW-Computer, und kehren Sie zum Windows PowerShell-Terminal zurück, indem Sie `exit` eingeben. Führen Sie dann den Befehl `Set-EflowVM` mit erhöhten Rechten in PowerShell aus. Geben Sie nach dem Ausführen des Befehls die gewünschten [Parameter](../../../iot-edge/reference-iot-edge-for-linux-on-windows-functions.md#set-eflowvm) ein, indem Sie den Anweisungen in PowerShell folgen, z. B. `cpuCount: 2`, `memoryInMB: 2048`. Stellen Sie die Edge-Module nach einigen Minuten erneut bereit, und aktivieren Sie die Livepipeline erneut zur Anzeige von Rückschlüssen. Wenn Verbindungsprobleme auftreten (z. B. Fehler 137 oder 255, die auf IoT Hub aufgeführt sind), müssen Sie diesen Schritt möglicherweise erneut ausführen. 
