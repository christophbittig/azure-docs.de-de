---
title: 'Device Update for Azure IoT Hub: Tutorial zum Verwenden des Ubuntu (18.04 x64) Simulator Reference Agent | Microsoft-Dokumentation'
description: Erste Schritte mit Device Update for Azure IoT Hub mit dem Ubuntu (18.04 x64) Simulator Reference Agent.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: d5a860455a66dc7db137b8d0eb7aad942271ef90
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566310"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Device Update for Azure IoT Hub: Tutorial zum Verwenden des Ubuntu (18.04 x64) Simulator Reference Agent

Device Update for IoT Hub unterstützt zwei Arten von Updates: imagebasiert und paketbasiert.

Imageupdates bieten ein höheres Maß an Vertrauen in den Endzustand des Geräts. Es ist in der Regel einfacher, die Ergebnisse eines Imageupdates zwischen einer Vorproduktions- und einer Produktionsumgebung zu replizieren, da dies nicht die gleichen Herausforderungen wie bei Paketen und deren Abhängigkeiten mit sich bringt. Aufgrund ihrer atomischen Natur kann auch ein A/B-Failovermodell problemlos übernommen werden.

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie ein imagebasiertes End-to-End-Update mithilfe von Device Update for IoT Hub ausführen. 

In diesem Lernprogramm lernen Sie Folgendes:
> [!div class="checklist"]
> * Herunterladen und Installieren des Image
> * Hinzufügen eines Tags zu Ihrem IoT-Gerät
> * Importieren eines Updates
> * Erstellen einer Gerätegruppe
> * Bereitstellen eines Imageupdates
> * Überwachen der Updatebereitstellung

## <a name="prerequisites"></a>Voraussetzungen
* Erstellen Sie [ein Konto und eine Instanz für Device Update](create-device-update-account.md) (einschließlich der Konfiguration einer IoT Hub-Instanz), sofern noch nicht geschehen.

### <a name="download-and-install"></a>Herunterladen und Installieren

* Az-Cmdlets (Azure CLI) für PowerShell:
  * Öffnen Sie PowerShell > Azure CLI installieren („Y“ für Eingabeaufforderungen zur Installation aus der „nicht vertrauenswürdigen“ Quelle).

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Aktivieren von WSL auf Ihrem Windows-Gerät (Windows-Subsystem für Linux)

1. Öffnen Sie PowerShell als Administrator auf dem Computer, und führen Sie den folgenden Befehl aus (Sie werden möglicherweise nach jedem Schritt zu einem Neustart aufgefordert, den Sie ausführen müssen):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Möglicherweise werden Sie nach diesem Schritt zu einem Neustart aufgefordert.* )

2. Besuchen Sie den Microsoft Store im Web, und installieren Sie [Ubuntu 18.04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Starten Sie „Ubuntu 18.04 LTS“, und installieren Sie die Distribution.

4. Nach der Installation werden Sie aufgefordert, den Stamm Namen für root (username) und das Kennwort festzulegen. Achten Sie darauf, dass Sie ein einprägsames Kennwort für den root-Namen verwenden.

5. Führen Sie in PowerShell den folgenden Befehl aus, um Ubuntu als standardmäßige Linux-Distribution festzulegen:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Listen Sie alle Linux-Distributionen auf, und vergewissern Sie sich, dass Ubuntu der Standardwert ist.

```powershell
PS> wsl --list
```

7. Folgendes sollte angezeigt werden: **Ubuntu-18.04 (Default)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Herunterladen des Device Update Ubuntu (18.04 x64) Simulator Reference Agent

Der Ubuntu-Referenz-Agent kann in den [Versionshinweisen](https://github.com/Azure/iot-hub-device-update/releases) im Abschnitt *Assets* heruntergeladen werden.

Es gibt zwei Versionen des Agents. Da Sie für dieses Tutorial das imagebasierte Szenario nutzen, verwenden Sie „AducIotAgentSim-microsoft-swupdate“. Wenn Sie stattdessen das paketbasierte Szenario nutzen möchten, müssen Sie „AducIotAgentSim-microsoft-apt“ verwenden.

## <a name="install-device-update-agent-simulator"></a>Installieren des Simulators des Device Update-Agents

1. Starten Sie Ubuntu WSL, und geben Sie den folgenden Befehl ein (beachten Sie das zusätzliche Leerzeichen und den Punkt am Ende).

  ```shell
  explorer.exe .
  ```

2. Kopieren Sie AducIotAgentSim-microsoft-swupdate (oder AducIotAgentSim-microsoft-apt) aus Ihrem lokalen Ordner (in den die Dateien unter /mnt in Ihren Basisordner in WSL heruntergeladen wurden).

3. Führen Sie den folgenden Befehl aus, um die Binärdateien ausführbar zu machen.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  oder

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Die Software Device Update for Azure IoT Hub unterliegt den folgenden Lizenzbedingungen:
   * [Lizenz für Device Update für IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Lizenz für den Übermittlungsoptimierungs-Client](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Lesen Sie vor Verwendung des Agents die Lizenzbedingungen. Durch die Installation und Verwendung erklären Sie Ihre Zustimmung zu diesen Bestimmungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie den Device Update for IoT Hub-Agent nicht.

## <a name="add-device-to-azure-iot-hub"></a>Hinzufügen eines Geräts zu Azure IoT Hub

Sobald der Device Update-Agent auf einem IoT-Gerät ausgeführt wird, muss das Gerät der Azure IoT Hub-Instanz hinzugefügt werden.  In Azure IoT Hub wird eine Verbindungszeichenfolge für ein bestimmtes Gerät generiert.

1. Starten Sie im Azure-Portal Device Update for IoT Hub.
2. Erstellen Sie ein neues Gerät.
3. Navigieren Sie auf der linken Seite der Seite zu „IoT-Geräte“, und wählen Sie „Neu“ aus.
4. Geben Sie unter „Device ID“ (Geräte-ID) einen Namen für das Gerät an. Stellen Sie sicher, dass das Kontrollkästchen „Autogenerate keys“(Schlüssel automatisch generieren) aktiviert ist.
5. Wählen Sie „Save“ (Speichern) aus.
6. Nun wird die Seite „Devices“ (Geräte) erneut angezeigt, und das von Ihnen erstellte Gerät sollte in der Liste enthalten sein. Wählen Sie dieses Gerät aus.
7. Wählen Sie in der Ansicht „Gerät“ das Symbol „Kopieren“ neben „Primary Connection String“ (Primäre Verbindungszeichenfolge) aus.
8. Fügen Sie die kopierten Zeichen irgendwo zur späteren Verwendung in den folgenden Schritte ein. **Diese kopierte Zeichenfolge ist Ihre Geräteverbindungszeichenfolge**.

## <a name="add-connection-string-to-simulator"></a>Hinzufügen der Verbindungszeichenfolge zum Simulator

Starten Sie den Device Update-Agent auf den neuen Softwaregeräten.

1. Starten Sie Ubuntu.
2. Führen Sie den Device Update-Agent aus, und geben Sie die Geräteverbindungszeichenfolge aus dem vorherigen Abschnitt (eingeschlossen in Hochkommas) an:

Ersetzen Sie `<device connection string>` durch Ihre Verbindungszeichenfolge.
```shell
sudo ./AducIotAgentSim-microsoft-swupdate "<device connection string>"
```

oder

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Scrollen Sie nach oben, und suchen Sie nach der Zeichenfolge, die angibt, dass sich das Gerät im Zustand „Leerlauf“ befindet. Der Zustand „Leerlauf“ bedeutet, dass das Gerät für Dienstbefehle bereit ist:

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Hinzufügen eines Tags zu Ihrem Gerät

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zum IoT Hub.

2. Suchen Sie im linken Navigationsbereich unter „IoT-Geräte“ oder „IoT Edge“ nach Ihrem IoT-Gerät, und navigieren Sie zum Gerätezwilling oder Modulzwilling.

3. Löschen Sie im Modulzwilling des Device Update-Agent-Moduls alle vorhandenen Device Update-Tagwerte, indem Sie diese auf NULL festlegen. Wenn Sie eine Geräteidentität mit dem Device Update-Agent verwenden, nehmen Sie diese Änderungen im Gerätezwilling vor.

4. Fügen Sie wie unten gezeigt einen neuen Device Update-Tagwert hinzu.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importieren des Updates

1. Laden Sie das [Beispielimportmanifest](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0/TutorialImportManifest_Sim.json) und das [Beispielimageupdate](https://github.com/Azure/iot-hub-device-update/releases/download/0.7.0-rc1/adu-update-image-raspberrypi3-0.6.5073.1.swu) herunter. _Hinweis_: Hierbei handelt es sich um erneut verwendete Updatedateien aus dem Raspberry Pi-Tutorial, da das Update in diesem Tutorial simuliert wird und daher der spezifische Dateiinhalt keine Rolle spielt. 
2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrer IoT Hub-Instanz mit Device Update. Wählen Sie anschließend auf der linken Navigationsleiste unter „Automatic Device Management“ (Automatische Geräteverwaltung) die Option „Device Updates“ (Geräteupdates) aus.

3. Wählen Sie die Registerkarte „Updates“ aus.

4. Wählen Sie „+ Import New Update“ (Neues Update importieren) aus.

5. Wählen Sie das Ordnersymbol oder das Textfeld unter „Select an Import Manifest File“ (Importmanifestdatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie das _Beispielimportmanifest_ aus, das Sie oben in Schritt 1 heruntergeladen haben.  Wählen Sie als Nächstes das Ordnersymbol oder das Textfeld unter „Select one or more update files“ (Mindestens eine Updatedatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie das _Beispielimageupdate_ aus, das Sie oben in Schritt 1 heruntergeladen haben. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot: Auswahl der Updatedatei." lightbox="media/import-update/select-update-files.png":::

6. Wählen Sie unter „Select a storage container“ (Speichercontainer auswählen) das Ordnersymbol oder das Textfeld aus. Wählen Sie dann das entsprechende Speicherkonto aus.

7. Wenn Sie bereits einen Container erstellt haben, können Sie ihn wiederverwenden. (Wählen Sie andernfalls „+ Container“ aus, um einen neuen Speichercontainer für Updates zu erstellen.)  Wählen Sie den Container aus, den Sie verwenden möchten, und klicken Sie auf „Select“ (Auswählen).
  
  :::image type="content" source="media/import-update/container.png" alt-text="Screenshot: Auswahl des Containers." lightbox="media/import-update/container.png":::

8. Wählen Sie „Submit“ (Übermitteln) aus, um den Importvorgang zu starten.

9. Der Importvorgang beginnt, und der Bildschirm ändert sich in den Abschnitt „Import History“ (Importverlauf). Wählen Sie „Refresh“ (Aktualisieren) aus, um den Fortschritt anzuzeigen, bis der Importvorgang abgeschlossen ist. Abhängig von der Größe des Updates kann dieser Vorgang in wenigen Minuten abgeschlossen werden, er kann aber auch länger dauern.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot: Updateimportsequenz." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wenn in der Spalte „Status“ angezeigt wird, dass der Import erfolgreich war, wählen Sie den Header „Ready to Deploy“ (Bereit für die Bereitstellung) aus. Ihr importiertes Update sollte nun in der Liste angezeigt werden.

[Weitere Informationen](import-update.md) zum Importieren von Updates.

## <a name="create-update-group"></a>Erstellen der Updategruppe

1. Navigieren Sie zu dem IoT Hub, den Sie zuvor mit Ihrer Device Update-Instanz verbunden haben.

2. Wählen Sie in der linken Navigationsleiste unter „Automatic Device Management“ (Automatische Geräteverwaltung) die Option „Device Updates“ aus.

3. Wählen Sie oben auf der Seite die Registerkarte „Groups“ (Gruppen) aus. 

4. Wählen Sie die Schaltfläche „Add“ (Hinzufügen) aus, um eine neue Gruppe zu erstellen.

5. Wählen Sie das IoT Hub-Tag aus der Liste aus, das Sie im vorherigen Schritt erstellt haben. Wählen Sie „Create update group“ (Updategruppe erstellen) aus.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot: Tagauswahl." lightbox="media/create-update-group/select-tag.PNG":::

[Weitere Informationen](create-update-group.md) zum Hinzufügen von Tags und zum Erstellen von Updategruppen


## <a name="deploy-update"></a>Bereitstellen des Updates

1. Nachdem die Gruppe erstellt wurde, sollte ein neues Update für Ihre Gerätegruppe mit einem Link zum Update unter „Pending Updates“ (Ausstehende Updates) angezeigt werden. Möglicherweise müssen Sie die Anzeige ein Mal aktualisieren. 

2. Klicken Sie auf das verfügbare Update.

3. Vergewissern Sie sich, dass die richtige Gruppe als Zielgruppe ausgewählt ist. Planen Sie die Bereitstellung, und wählen Sie dann „Deploy update“ (Update bereitstellen) aus.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Auswählen des Updates" lightbox="media/deploy-update/select-update.png":::

4. Zeigen Sie das Konformitätsdiagramm an. Es sollte angezeigt werden, dass das Update jetzt ausgeführt wird. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Update wird ausgeführt." lightbox="media/deploy-update/update-in-progress.png":::

5. Nachdem das Gerät erfolgreich aktualisiert wurde, sollten Sie sehen, dass Ihr Konformitätsdiagramm und die Details der Bereitstellung so aktualisiert werden, dass sie übereinstimmen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update erfolgreich" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Überwachen einer Updatebereitstellung

1. Wählen Sie oben auf der Seite die Registerkarte „Deployments“ (Bereitstellungen) aus.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Registerkarte „Deployments“ (Bereitstellungen)" lightbox="media/deploy-update/deployments-tab.png":::

2. Wählen Sie die erstellte Bereitstellung aus, um die Bereitstellungsdetails anzuzeigen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Bereitstellungsdetails" lightbox="media/deploy-update/deployment-details.png":::

3. Wählen Sie „Refresh“ (Aktualisieren) aus, um die neuesten Statusdetails anzuzeigen. Setzen Sie diesen Prozess fort, bis der Status in „Succeeded“ (Erfolgreich) geändert wurde.

Sie haben jetzt eine erfolgreiches End-to-End-Imageupdate mithilfe von Device Update for IoT Hub durchgeführt, indem Sie den Simulator Reference Agent für Ubuntu (18.04 x64) verwendet haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie es nicht mehr benötigten, bereinigen Sie Ihr Konto für das Geräteupdate sowie die IoT Hub-Instanz und das IoT-Gerät. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Problembehandlung](troubleshoot-device-update.md)

