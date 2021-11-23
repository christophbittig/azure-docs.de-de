---
title: 'Device Update for Azure IoT Hub: Tutorial zum Verwenden des Ubuntu Server 18.04 x64 Package Agent | Microsoft-Dokumentation'
description: Erste Schritte mit Device Update for Azure IoT Hub mit dem Ubuntu Server 18.04 x64 Package Agent.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 1cd4ea0d01299b8e361e65a1f8152c4222df3bf0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372168"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Device Update for Azure IoT Hub: Tutorial zum Verwenden des Paket-Agents unter Ubuntu Server 18.04 x64

Device Update for IoT Hub unterstützt zwei Arten von Updates: imagebasiert und paketbasiert.

Paketbasierte Updates sind gezielte Updates, die nur eine bestimmte Komponente oder Anwendung auf dem Gerät ändern. Dies führt zu einem geringeren Verbrauch an Bandbreite und verringert die Zeit zum Herunterladen und Installieren des Updates. Paketbasierte Updates ermöglichen in der Regel auch eine geringere Ausfallzeit der Geräte beim Anwenden eines Updates und vermeiden den Mehraufwand beim Erstellen von Images. Sie verwenden ein [APT-Manifest](device-update-apt-manifest.md), das dem Device Update-Agent die Informationen zur Verfügung stellt, die er benötigt, um die in der APT-Manifestdatei angegebenen Pakete (sowie ihre Abhängigkeiten) aus einem bestimmten Repository herunterzuladen und zu installieren.

In diesem End-to-End-Tutorial werden Sie durch das Aktualisieren von Azure IoT Edge unter Ubuntu Server 18.04 x64 mithilfe des Device Update-Paket-Agents geführt. In diesem Tutorial wird zwar die Aktualisierung von IoT Edge veranschaulicht, mit ähnlichen Schritten können sie aber auch andere Pakete wie die verwendete Container-Engine aktualisieren.

Die Tools und Konzepte in diesem Tutorial gelten weiterhin, auch wenn Sie beabsichtigen, eine andere Konfiguration der Betriebssystemplattform zu verwenden. Vervollständigen Sie diese Einführung in einen End-to-End-Updateprozess, und wählen Sie dann Ihre bevorzugte Aktualisierungsform und Betriebssystemplattform aus, um sich mit den Details vertraut zu machen.

In diesem Lernprogramm lernen Sie Folgendes:
> [!div class="checklist"]
> * Herunterladen und Installieren des Device Update-Agents und seiner Abhängigkeiten
> * Hinzufügen eines Tags zu Ihrem Gerät
> * Importieren eines Updates
> * Erstellen einer Gerätegruppe
> * Bereitstellen eines Paketupdates
> * Überwachen der Updatebereitstellung

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie [ein Konto und eine Instanz für Device Update](create-device-update-account.md) (einschließlich der Konfiguration einer IoT Hub-Instanz), sofern noch nicht geschehen.
* Die [Verbindungszeichenfolge für ein IoT Edge-Gerät](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-provisioning-information)

## <a name="prepare-a-device"></a>Vorbereiten eines Geräts
### <a name="using-the-automated-deploy-to-azure-button"></a>Verwenden der Schaltfläche für die automatisierte Bereitstellung in Azure

Der Einfachheit halber wird in diesem Tutorial eine [Cloud-Init](../virtual-machines/linux/using-cloud-init.md)-basierte [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) verwendet, mit der Sie schnell einen virtuellen Computer mit Ubuntu 18.04 LTS einrichten können. Mit dieser Vorlage werden die Azure IoT Edge-Runtime und der Device Update-Paket-Agent installiert. Anschließend wird das Gerät mithilfe der Geräteverbindungszeichenfolge für ein von Ihnen bereitgestelltes Iot Edge Gerät (erforderlich) automatisch mit Bereitstellungsinformationen konfiguriert. Mit der Azure Resource Manager-Vorlage entfällt außerdem die Notwendigkeit, zum Abschließen des Setups eine SSH-Sitzung zu starten.

1. Klicken Sie zunächst auf die folgende Schaltfläche:

   [![Schaltfläche „In Azure bereitstellen“ für „iotedge-vm-deploy“](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Füllen Sie im neu gestarteten Fenster die verfügbaren Formularfelder aus:

    > [!div class="mx-imgBorder"]
    > [![Screenshot der Vorlage „iotedge-vm-deploy“](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Abonnement**: Das aktive Azure-Abonnement, in dem der virtuelle Computer bereitgestellt werden soll.

    **Ressourcengruppe**: Eine vorhandene oder neu erstellte Ressourcengruppe, die den virtuellen Computer und die ihm zugeordneten Ressourcen enthalten soll.

    **Präfix der DNS-Bezeichnung**: Ein erforderlicher Wert Ihrer Wahl, der dem Hostnamen des virtuellen Computers vorangestellt werden soll.

    **Administratorbenutzername**: Ein Benutzername, der Rootberechtigungen für die Bereitstellung erhält.

    **Geräte-Verbindungszeichenfolge**: Eine [Geräte-Verbindungszeichenfolge](../iot-edge/how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information) für ein Gerät, das in Ihrem vorgesehenen [IoT Hub](../iot-hub/about-iot-hub.md) erstellt wurde.

    **VM-Größe**: Die [Größe](../cloud-services/cloud-services-sizes-specs.md) des bereitzustellenden virtuellen Computers

    **Ubuntu-Betriebssystemversion**: Die Version des Ubuntu-Betriebssystems, die auf dem virtuellen Basiscomputer installiert werden soll. Behalten Sie den Standardwert bei, da er bereits auf „Ubuntu 18.04-LTS“ festgelegt ist.

    **Standort**: Die [geografische Region](https://azure.microsoft.com/global-infrastructure/locations/), in der der virtuelle Computer bereitgestellt werden soll. Dieser Wert wird standardmäßig auf den Speicherort der ausgewählten Ressourcengruppe festgelegt.

    **Authentifizierungstyp**: Wählen Sie je nach Ihrer Einstellung **sshPublicKey** oder **password** aus.

    **Administratorkennwort oder Schlüssel**: Der Wert des öffentlichen SSH-Schlüssels oder der Wert des Kennworts, je nach der Auswahl des Authentifizierungstyps.

    Nachdem Sie alle Felder ausgefüllt haben, aktivieren Sie das Kontrollkästchen unten auf der Seite zum Akzeptieren der Bedingungen, und wählen Sie **Kauf** aus, um die Bereitstellung zu starten.

1. Überprüfen Sie, ob die Bereitstellung erfolgreich abgeschlossen wurde. Warten Sie nach Abschluss der Bereitstellung einige Minuten, bis die Nachinstallation und die Konfiguration abgeschlossen sind, bevor Sie IoT Edge und den Device Update-Paket-Agent installieren.

   Eine virtuelle Computerressource sollte in der ausgewählten Ressourcengruppe bereitgestellt worden sein.  Notieren Sie sich den Computernamen, der das Format `vm-0000000000000` haben sollte. Notieren Sie sich außerdem den zugeordneten **DNS-Namen**, der das Format „`<dnsLabelPrefix>`.`<location>`cloudapp.azure.com“ haben sollte.

    Der **DNS-Name** kann im Azure-Portal aus dem Abschnitt **Übersicht** des neu bereitgestellten virtuellen Computers abgerufen werden.

    > [!div class="mx-imgBorder"]
    > [![Screenshot mit dem DNS-Namen der IoT Edge-VM](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Wenn Sie nach dem Setup eine SSH-Verbindung mit diesem virtuellen Computer herstellen möchten, verwenden Sie den zugeordneten **DNS-Namen** mit folgendem Befehl: `ssh <adminUsername>@<DNS_Name>`.

### <a name="optional-manually-prepare-a-device"></a>(Optional) Manuelles Vorbereiten eines Geräts
Die folgenden manuellen Schritte zum Installieren und Konfigurieren des Geräts ähneln den Schritten, die durch [dieses cloud-init-Skript](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt) automatisiert wurden. Diese Schritte können zum Vorbereiten eines physischen Geräts verwendet werden.

1. Befolgen Sie die Anleitung unter [Installieren oder Deinstallieren von Azure IoT Edge für Linux](../iot-edge/how-to-provision-single-device-linux-symmetric.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > Der Device Update-Paket-Agent ist nicht von IoT Edge abhängig. Er greift jedoch auf den Daemon für den IoT-Identitätsdienst zu, der mit IoT Edge (ab 1.2.0) installiert wird, um eine Identität abzurufen und eine Verbindung mit IoT Hub herzustellen.
   >
   > Der [Daemon für den IoT Identitätsdienst kann eigenständig auf Linux-basierten IoT-Geräten installiert werden](https://azure.github.io/iot-identity-service/installation.html). Dies wird in diesem Tutorial jedoch nicht erläutert. Die Reihenfolge der Installation ist wichtig. Der Device Update-Paket-Agent muss _nach_ dem IoT-Identitätsdienst installiert werden. Andernfalls wird der Paket-Agent nicht als autorisierte Komponente registriert, um eine Verbindung mit IoT Hub herzustellen.

1. Installieren Sie dann die DEB-Pakete des Device Update-Agents.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Softwarepakete von Device Update for Azure IoT Hub unterliegen den folgenden Lizenzbedingungen:
  * [Lizenz für Device Update für IoT Hub](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Lizenz für den Übermittlungsoptimierungs-Client](https://github.com/microsoft/do-client/blob/main/LICENSE)

Lesen Sie die Lizenzbedingungen, bevor Sie ein Paket verwenden. Durch Ihre Installation und Verwendung eines Pakets erklären Sie Ihre Zustimmung zu diesen Bedingungen. Wenn Sie den Lizenzbedingungen nicht zustimmen, verwenden Sie das Paket nicht.

## <a name="add-a-tag-to-your-device"></a>Hinzufügen eines Tags zu Ihrem Gerät

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zum IoT Hub.

2. Suchen Sie im linken Navigationsbereich unter „IoT Edge“ nach Ihrem IoT Edge-Gerät, und navigieren Sie zum Gerätezwilling oder Modulzwilling.

3. Löschen Sie im Modulzwilling des Device Update-Agent-Moduls alle vorhandenen Device Update-Tagwerte, indem Sie diese auf NULL festlegen. Wenn Sie eine Geräteidentität mit dem Device Update-Agent verwenden, nehmen Sie diese Änderungen im Gerätezwilling vor.

4. Fügen Sie wie unten gezeigt einen neuen Device Update-Tagwert hinzu.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importieren des Updates

1. Navigieren Sie zu [Device Update-Releases](https://github.com/Azure/iot-hub-device-update/releases) auf GitHub, und klicken Sie auf das Dropdownmenü „Assets“.

3. Klicken Sie auf `Edge.package.update.samples.zip`, um die ZIP-Datei herunterzuladen.

5. Extrahieren Sie den Inhalt des Ordners, um ein Beispiel für ein [APT-Manifest](device-update-apt-manifest.md) und das zugehörige [Importmanifest](import-concepts.md) zu entdecken. 

2. Wählen Sie im Azure-Portal in der linken Navigationsleiste in Ihrem IoT Hub unter „Automatic Device Management“ (Automatische Geräteverwaltung) die Option „Device Updates“ aus.

3. Wählen Sie die Registerkarte „Updates“ aus.

4. Wählen Sie „+ Import New Update“ (Neues Update importieren) aus.

5. Wählen Sie das Ordnersymbol oder das Textfeld unter „Select an Import Manifest File“ (Importmanifestdatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie das Importmanifest `sample-1.0.1-aziot-edge-importManifest.json` in dem zuvor heruntergeladenen Ordner aus. Wählen Sie als Nächstes das Ordnersymbol oder das Textfeld unter „Select one or more update files“ (Mindestens eine Updatedatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie die apt-Manifestupdatedatei `sample-1.0.1-aziot-edge-apt-manifest.json` in dem zuvor heruntergeladenen Ordner aus.
Mit diesem Update werden die Pakete `aziot-identity-service` und `aziot-edge` auf Ihrem Gerät auf Version 1.2.0~rc4-1 aktualisiert.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Screenshot: Auswahl der Updatedatei." lightbox="media/import-update/select-update-files.png":::

6. Wählen Sie unter „Select a storage container“ (Speichercontainer auswählen) das Ordnersymbol oder das Textfeld aus. Wählen Sie dann das entsprechende Speicherkonto aus.

7. Wenn Sie bereits einen Container erstellt haben, können Sie ihn wiederverwenden. (Wählen Sie andernfalls „+ Container“ aus, um einen neuen Speichercontainer für Updates zu erstellen.)  Wählen Sie den Container aus, den Sie verwenden möchten, und klicken Sie auf „Select“ (Auswählen).

   :::image type="content" source="media/import-update/container.png" alt-text="Screenshot: Auswahl des Containers." lightbox="media/import-update/container.png":::

8. Wählen Sie „Submit“ (Übermitteln) aus, um den Importvorgang zu starten.

9. Der Importvorgang beginnt, und der Bildschirm ändert sich in den Abschnitt „Import History“ (Importverlauf). Wählen Sie „Refresh“ (Aktualisieren) aus, um den Fortschritt anzuzeigen, bis der Importvorgang abgeschlossen ist. Abhängig von der Größe des Updates kann der Importvorgang in wenigen Minuten abgeschlossen sein, er kann aber auch länger dauern.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Screenshot: Updateimportsequenz." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wenn in der Spalte „Status“ angezeigt wird, dass der Import erfolgreich war, wählen Sie den Header „Ready to Deploy“ (Bereit für die Bereitstellung) aus. Ihr importiertes Update sollte nun in der Liste angezeigt werden.

[Weitere Informationen](import-update.md) zum Importieren von Updates.

## <a name="create-update-group"></a>Erstellen der Updategruppe

1. Navigieren Sie zu dem IoT Hub, den Sie zuvor mit Ihrer Device Update-Instanz verbunden haben.

1. Wählen Sie in der linken Navigationsleiste unter „Automatic Device Management“ (Automatische Geräteverwaltung) die Option „Device Updates“ aus.

1. Wählen Sie oben auf der Seite die Registerkarte „Groups“ (Gruppen) aus.

1. Wählen Sie die Schaltfläche „Add“ (Hinzufügen) aus, um eine neue Gruppe zu erstellen.

1. Wählen Sie das IoT Hub-Tag aus der Liste aus, das Sie im vorherigen Schritt erstellt haben. Wählen Sie „Create update group“ (Updategruppe erstellen) aus.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot: Tagauswahl." lightbox="media/create-update-group/select-tag.PNG":::

[Weitere Informationen](create-update-group.md) zum Hinzufügen von Tags und zum Erstellen von Updategruppen

## <a name="deploy-update"></a>Bereitstellen des Updates

1. Nachdem die Gruppe erstellt wurde, sollte ein neues Update für Ihre Gerätegruppe mit einem Link zum Update in der Spalte _Verfügbare Updates_ angezeigt werden. Möglicherweise müssen Sie die Anzeige ein Mal aktualisieren.

1. Klicken Sie auf den Link zum verfügbaren Update.

1. Vergewissern Sie sich, dass die richtige Gruppe als Zielgruppe ausgewählt ist, und planen Sie die Bereitstellung.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Auswählen des Updates" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Das Startdatum bzw. die Startzeit liegt standardmäßig 24 Stunden nach der aktuellen Uhrzeit. Wählen Sie unbedingt ein anderes Datum bzw. eine andere Uhrzeit aus, wenn die Bereitstellung früher beginnen soll.

1. Wählen Sie „Deploy update“ (Update bereitstellen) aus.

1. Zeigen Sie das Konformitätsdiagramm an. Es sollte angezeigt werden, dass das Update jetzt ausgeführt wird. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Update wird ausgeführt." lightbox="media/deploy-update/update-in-progress.png":::

1. Nachdem das Gerät erfolgreich aktualisiert wurde, sollten Sie sehen, dass Ihr Konformitätsdiagramm und die Details der Bereitstellung so aktualisiert werden, dass sie übereinstimmen. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Update erfolgreich" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Überwachen einer Updatebereitstellung

1. Wählen Sie oben auf der Seite die Registerkarte „Deployments“ (Bereitstellungen) aus.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Registerkarte „Deployments“ (Bereitstellungen)" lightbox="media/deploy-update/deployments-tab.png":::

1. Wählen Sie die erstellte Bereitstellung aus, um die Bereitstellungsdetails anzuzeigen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Bereitstellungsdetails" lightbox="media/deploy-update/deployment-details.png":::

1. Wählen Sie „Refresh“ (Aktualisieren) aus, um die neuesten Statusdetails anzuzeigen. Setzen Sie diesen Prozess fort, bis der Status in „Succeeded“ (Erfolgreich) geändert wurde.

Sie haben nun ein erfolgreiches End-to-End-Paketupdate mit Device Update für IoT Hub auf einem Ubuntu Server 18.04 x64-Gerät abgeschlossen. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Device Update-Konto, die Device Update- und die IoT Hub-Instanz und das IoT Edge-Gerät (falls Sie den virtuellen Computer über die Schaltfläche „In Azure bereitstellen“ erstellt haben) nicht mehr benötigen, können Sie diese Komponenten bereinigen. Sie können zu diesem Zweck zu jeder einzelnen Ressource navigieren und dann „Löschen“ auswählen. Sie müssen eine Geräteupdateinstanz bereinigen, bevor Sie das Geräteupdatekonto bereinigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Imageupdate auf Raspberry Pi 3 B+](device-update-raspberry-pi.md)
