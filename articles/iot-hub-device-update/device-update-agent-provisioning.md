---
title: Bereitstellen des Device Update for Azure IoT Hub-Agents | Microsoft-Dokumentation
description: Bereitstellen des Device Update for Azure IoT Hub-Agents
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ecfbd8ac385a3449ce2934ec93b10c255d621fe1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259073"
---
# <a name="device-update-agent-provisioning"></a>Bereitstellen des Geräte-Update-Agents

Der Modul-Agent für Geräte-Updates kann zusammen mit anderen Systemprozessen und [IOT Edge Modulen](../iot-edge/iot-edge-modules.md) ausgeführt werden, die eine Verbindung mit Ihrem IOT Hub als Teil desselben logischen Geräts herstellen. In diesem Abschnitt wird beschrieben, wie Sie den Geräte-Update-Agent als Modulkennung bereitstellen. 


## <a name="module-identity-vs-device-identity"></a>Modulkennung im Vergleich zur Geräteidentität

In IoT Hub können unter jeder Geräteidentität bis zu 50 Modulidentitäten erstellt werden. Jede Modulidentität generiert implizit einen Modulzwilling. Auf der Geräteseite können Sie mithilfe der IoT Hub-Geräte-SDKs Module erstellen, die jeweils eine unabhängige Verbindung mit IoT Hub herstellen. Modulidentität und Modulzwilling bieten die gleichen Funktionen wie Geräteidentität und Gerätezwilling, aber mit einer höheren Granularität. [Erfahren Sie mehr über Modulidentitäten in Azure IoT Hub](../iot-hub/iot-hub-devguide-module-twins.md)

Wenn Sie von einem Agent auf Geräteebene migrieren, um den Agent als Modulidentität auf dem Gerät hinzuzufügen, entfernen Sie den älteren Agent, der über den Gerätezwilling kommuniziert hat. Wenn Sie den Geräteupdate-Agent als Modulidentität bereitstellen, erfolgt die gesamte Kommunikation zwischen dem Gerät und dem Geräteupdatedienst über den Modulzwilling. Denken Sie also daran: Der Modulzwilling des Geräts muss beim Erstellen von [Gruppen](device-update-groups.md) markiert werden und die gesamte [Kommunikation](device-update-plug-and-play.md) über den Modulzwilling erfolgen.

## <a name="support-for-device-update"></a>Unterstützung für Geräteobjekte

Die folgenden IOT-Gerätetypen werden derzeit bei der Geräte Aktualisierung unterstützt:

* Linux-Geräte (IOT Edge und nicht IOT Edge Geräte):
    * Image A/B-Update:
        - Yocto-ARM64 (Verweis-Image), erweiterbar über Open Source, um [Ihre eigenen Images](device-update-agent-provisioning.md#how-to-build-and-run-device-update-agent) für andere Architekturen nach Bedarf zu erstellen.
        - Ubuntu 18.04 Simulator
       
    * Der Paketagent unterstützt Builds für die folgenden Plattformen/Architekturen:
        - Ubuntu Server 18.04 x64 Packetagent 
        - Debian 9 
        
* Eingeschränkte Geräte:
    * Azurertos Geräte Update-Agent-Beispiele: [Geräte Update für Azure IOT Hub Tutorial für Azure-Real-Time-Operating-System](device-update-azure-real-time-operating-system.md)

* Getrennte Geräte: 
    * [Grundlegendes zur Unterstützung für getrennte Geräteupdates](connected-cache-disconnected-device-update.md)


## <a name="prerequisites"></a>Voraussetzungen  

Wenn Sie das IOT-Gerät/IOT Edge Gerät für [paketbasierte Updates](./understand-device-update.md#support-for-a-wide-range-of-update-artifacts)einrichten, fügen Sie Packages.Microsoft.com den Depots Ihres Computers hinzu, indem Sie die folgenden Schritte ausführen:

1. Melden Sie sich auf dem Computer oder dem IOT-Gerät an, auf dem Sie den Agent für die Geräteaktualisierung installieren möchten.

1. Öffnen Sie ein Terminalfenster.

1. Installieren Sie die zum Betriebssystem Ihres Geräts passende Repositorykonfiguration.

    ```shell
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```
    
1. Kopieren Sie die generierte Liste in das Verzeichnis „sources.list.d“.

    ```shell
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```
    
1. Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

    ```shell
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    ```

    ```shell
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

## <a name="how-to-provision-the-device-update-agent-as-a-module-identity"></a>So stellen Sie den Geräte-Update-Agenten als Modulkennung bereit

In diesem Abschnitt wird beschrieben, wie Sie den Device Update-Agent als Modulkennung auf folgenden Geräten bereitstellen: 
* IoT Edge-fähige Geräte 
* Geräte ohne Edge IoT
* andere IoT-Geräte 
 
Führen Sie die Schritte in allen oder einigen der folgenden Abschnitte aus, um den Device Update-Agent basierend auf dem Typ des von Ihnen verwalteten IoT-Geräts hinzuzufügen. 

### <a name="on-iot-edge-enabled-devices"></a>Auf IOT Edge aktivierten Geräten

Befolgen Sie diese Anweisungen, um den Geräte-Update-Agent auf [IOT Edge aktivierten Geräten](../iot-edge/index.yml)bereitzustellen.

1. Führen Sie die Schritte unter [Manuelles Bereitstellen eines einzelnen Linux-IoT Edge-Geräts](../iot-edge/how-to-provision-single-device-linux-symmetric.md?preserve-view=true&view=iotedge-2020-11) aus.

1. Installieren Sie den Device Update-Agent für Imageupdates.

    Beispielimages finden Sie im Repository [Artefakte](https://github.com/Azure/iot-hub-device-update/releases). Die Datei vom Typ „swUpdate“ ist das Basisimage, das Sie auf eine Raspberry Pi B3+-Platine einspielen können. Die GZ-Datei ist das Update, das Sie über Device Update for IoT Hub importieren. Ein Beispiel finden Sie unter [Einspielen des Images auf die SD-Karte](./device-update-raspberry-pi.md#flash-sd-card-with-image).  

1. Installieren Sie den Device Update-Agent für Paketupdates.

    - Für die aktuellen Agent-Versionen von packages.miscrosoft.com: Aktualisieren Sie Paketlisten auf Ihrem Gerät, und installieren Sie das Device Update-Agent-Paket und seine Abhängigkeiten mit:   

        ```shell
        sudo apt-get update
        ```
    
        ```shell
        sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
        ```
    
    - Für alle Release Candidate-Agent-Versionen (‚rc‘) von [Artifacts](https://github.com/Azure/iot-hub-device-update/releases): Laden Sie die DEP-Datei auf den Computer herunter, auf dem Sie den Device Update-Agent installieren möchten, und dann:
   
        ```shell
        sudo apt-get install -y ./"<PATH TO FILE>"/"<.DEP FILE NAME>"
        ```
    
1. Nun können Sie den Device Update-Agent auf dem IoT Edge-Gerät starten. 

### <a name="on-non-edge-iot-linux-devices"></a>Auf nicht-Edge-IOT-Linux-Geräten

Befolgen Sie diese Anweisungen, um den Geräte-Update-Agent auf IOT Edge aktivierten Geräten bereitzustellen.

1. Installieren Sie den IOT Identitätsdienst, und fügen Sie dem IOT-Gerät die neueste Version hinzu. 
    1. Melden Sie sich auf dem Computer oder dem IOT-Gerät an.
    1. Öffnen Sie ein Terminalfenster.
    1.  Installieren Sie mit dem folgenden Befehl den neuesten [IOT-Identitätsdienst](https://github.com/Azure/iot-identity-service/blob/main/docs-dev/packaging.md#installing-and-configuring-the-package) auf Ihrem IOT-Gerät:
    > [!Note]
    > Der IOT Identitätsdienst registriert Modul Identitäten mit IOT Hub, indem derzeit symmetrische Schlüssel verwendet werden.

    ```shell
    sudo apt-get install aziot-identity-service
    ```
        
1. Der IOT-Identitätsdienst wird bereitgestellt, um die IOT-Geräteinformationen zu erhalten.

    Erstellen Sie eine benutzerdefinierte Kopie der Konfigurationsvorlage, damit wir die Bereitstellungsinformationen hinzufügen können. Geben Sie in einem Terminal den folgenden Befehl ein:
      
    ```shell
    sudo cp /etc/aziot/config.toml.template /etc/aziot/config.toml 
    ```
   
1. Bearbeiten Sie als nächstes die Konfigurationsdatei, um die Verbindungszeichenfolge des Geräts einzuschließen, das Sie als Bereitstellungsziel für dieses Gerät oder diesen Computer fungieren möchten. Geben Sie die folgenden Befehle in das Terminal ein.

    ```shell
    sudo nano /etc/aziot/config.toml
    ```
   
1. Sie sollten eine Meldung wie im folgenden Beispiel sehen:

    :::image type="content" source="media/understand-device-update/config.png" alt-text="Diagramm der Konfigurationsdatei für den IOT-Identitätsdienst." lightbox="media/understand-device-update/config.png":::

    1. Suchen Sie im gleichen Nano-Fenster den Block mit der „manuellen Bereitstellung mit der Verbindungszeichenfolge“.
    1. Löschen Sie im Fenster das „#“-Symbol vor „Bereitstellung“
    1. Löschen Sie im Fenster das „#“-Symbol vor „Quelle“ 
    1. Löschen Sie im Fenster das „#“-Symbol vor „CONNECTION_STRING“
    1. Löschen Sie im Fenster die Zeichenkette innerhalb der Anführungszeichen rechts von „connection_string“ und fügen Sie dort Ihre Verbindungszeichenkette ein 
    1. Speichern Sie die Änderungen an der Datei mit „STRG + X“ und dann „Y“, und drücken Sie die EINGABETASTE, um die Änderungen zu speichern. 
    
1. Wenden Sie nun den IOT-Identitätsdienst mithilfe des folgenden Befehls an und starten Sie ihn neu. Nun sollte Folgendes angezeigt werden: „Abgeschlossen“: Ausdruck bedeutet, dass Sie den IOT Identitätsdienst erfolgreich konfiguriert haben.

    > [!Note]
    > Der IOT Identitätsdienst registriert Modul Identitäten mit IOT Hub, indem derzeit symmetrische Schlüssel verwendet werden.
    
    ```shell
    sudo aziotctl config apply
    ```
    
1. Installieren Sie schließlich den Device Update-Agent. Beispielimages werden in [Artifacts](https://github.com/Azure/iot-hub-device-update/releases) bereitgestellt, die Datei vom Typ „swUpdate“ ist das Basisimage, das Sie auf eine Raspberry Pi B3+-Platine einspielen können, und die GZ-Datei ist das Update, das Sie über Device Update for IoT Hub importieren. Sehen Sie sich ein [Beispiel für das Einspielen des Images auf Ihrem IoT Hub-Gerät](./device-update-raspberry-pi.md#flash-sd-card-with-image) an.

1. Nun können Sie den Device Update-Agent auf dem IoT-Gerät starten. 

### <a name="other-iot-devices"></a>Andere IOT-Geräte

Der Agent für die Geräteaktualisierung kann auch ohne den IOT-Identitätsdienst zum Testen oder für eingeschränkte Geräte konfiguriert werden. Führen Sie die folgenden Schritte aus, um den Geräte-Update-Agent mithilfe einer Verbindungszeichenfolge (vom Modul oder Gerät) bereitzustellen.

1. Beispielimages finden Sie im Repository [Artefakte](https://github.com/Azure/iot-hub-device-update/releases). Die Datei vom Typ „swUpdate“ ist das Basisimage, das Sie auf eine Raspberry Pi B3+-Platine einspielen können. Die GZ-Datei ist das Update, das Sie über Device Update for IoT Hub importieren. Ein Beispiel finden Sie unter [Einspielen des Images auf die SD-Karte](./device-update-raspberry-pi.md#flash-sd-card-with-image).

1. Melden Sie sich auf dem Computer oder IOT Edge Gerät/IOT-Gerät an.
    
1. Öffnen Sie ein Terminalfenster.

1. Fügen Sie die Verbindungszeichenfolge zur [Konfigurationsdatei des Geräte-Updates hinzu](device-update-configuration-file.md):

    1. Geben Sie Folgendes in das Terminalfenster ein:
   
        - Für die Verwendung von [Paketupdates](device-update-ubuntu-agent.md): sudo nano /etc/adu/adu-conf.txt
        - Für die Verwendung von [Imageupdates](device-update-raspberry-pi.md): sudo nano /adu/adu-conf.txt
       
    1. Es sollte sich ein Fenster öffnen, in dem sich ein Text befindet. Löschen Sie die gesamte Zeichenfolge nach „connection_String =“, wenn Sie den Geräte Update-Agent zum ersten Mal auf dem IOT-Gerät bereitstellen. Es ist nur ein Platzhaltertext.
    
    1. Ersetzen Sie im Terminal \<your-connection-string\> durch die Verbindungszeichenfolge des Geräts für Ihre Instanz des Device Update-Agents. Drücken Sie die EINGABETASTE, und wählen Sie dann **Speichern** aus. Es sollte wie im folgenden Beispiel aussehen:
    
        ```text
        connection_string=<ADD CONNECTION STRING HERE>
        ```   
        
    > [!Important]
    > Fügen Sie keine Anführungszeichen um die Verbindungszeichenfolge hinzu.
    
1. Nun können Sie den Device Update-Agent auf dem IoT-Gerät starten. 


## <a name="how-to-start-the-device-update-agent"></a>Starten des Geräte-Update-Agents

In diesem Abschnitt wird beschrieben, wie Sie den Geräte-Update-Agent als Modulkennung starten und überprüfen, die auf Ihrem IOT-Gerät erfolgreich ausgeführt wird.

1. Melden Sie sich bei dem Computer oder Gerät an, auf dem der Geräte-Update-Agent installiert ist.

1. Öffnen Sie ein Terminal-Fenster, und geben Sie den folgenden Befehl ein.

    ```shell
    sudo systemctl restart adu-agent
    ```
    
1. Sie können den Status des Agenten mit dem folgenden Befehl überprüfen. Wenn Probleme auftreten, finden Sie weitere Informationen in diesem [Handbuch zur Problembehandlung](troubleshoot-device-update.md).
    
    ```shell
    sudo systemctl status adu-agent
    ```
    
    Als Status sollte angezeigt werden.

1. Navigieren Sie im IOT Hub-Portal zu IOT-Gerät oder IOT Edge Geräte, um das Gerät zu finden, das Sie mit dem Geräte-Update-Agent konfiguriert haben. Dort sehen Sie, dass der Device-Update-Agent als Modul ausgeführt wird. Beispiel:

    :::image type="content" source="media/understand-device-update/device-update-module.png " alt-text="Diagramm des Modulnamens des Geräte-Updates." lightbox="media/understand-device-update/device-update-module.png":::


## <a name="how-to-build-and-run-device-update-agent"></a>Erstellen und Ausführen des Geräte-Update-Agents

Sie können auch ihren eigenen Geräte-Update-Agent für Kunden erstellen und ändern.

Befolgen Sie die Anweisungen, um den Device Update-Agent aus der Quelle zu [erstellen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).

Nachdem der Agent erfolgreich erstellt wurde, ist es an der Zeit, ihn [auszuführen](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md).

Nehmen Sie nun die Änderungen vor, die für die Einbindung des Agents in Ihr Image erforderlich sind.  Anleitungen finden Sie unter der Vorgehensweise zum [Ändern](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.md) des Device Update-Agents.


## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung

Wenn Probleme auftreten, lesen Sie den [Leitfaden zur Problembehandlung](troubleshoot-device-update.md) für Device Update for IoT Hub, um mögliche Probleme zu beheben und die erforderlichen Informationen für Microsoft zu erfassen.


## <a name="next-steps"></a>Nächste Schritte

Sie können die folgenden vorgefertigten Bilder und Binärdateien für eine einfache Demonstration von Device Update for IoT Hub verwenden:

- [Imageupdate: Erste Schritte mit Raspberry Pi 3 B+ Referenz Yocto Image](device-update-raspberry-pi.md) ist über Open Source erweiterbar, um bei Bedarf eigene Bilder für andere Architekturen zu erstellen.

- [Imageupdate: Erste Schritte mit Ubuntu (18.04 x64) Simulator Reference Agent](device-update-simulator.md)

- [Paketaktupdate: Erste Schritte mit Ubuntu Server 18.04 x64 Package-Agent](device-update-ubuntu-agent.md)

- [Geräte-Update für Azure IoT Hub Tutorial für Azure-Echtzeit-Betriebssystem](device-update-azure-real-time-operating-system.md)
