---
title: Hinzufügen eines neuen Updates | Microsoft-Dokumentation
description: Schrittanleitung zum Hinzufügen eines neuen Updates in Device Update for IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: caa58c1affc9e4a9cc9d3ec5b63e0b396e289144
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370420"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Hinzufügen eines Updates zu Device Update for IoT Hub
Hier erfahren Sie, wie Sie in Device Update for IoT Hub ein neues Update hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

* [Zugriff auf einen IoT Hub mit aktiviertem Device Update for IoT Hub](create-device-update-account.md). 
* Ein [für Device Update bereitgestelltes](device-update-agent-provisioning.md) IoT-Gerät (oder ein entsprechender Simulator) in IoT Hub.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) oder später (einschließlich Linux-, macOS-und Windows-Installationen)
* Unterstützte Browser:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Einige an diesen Dienst übermittelte Daten werden möglicherweise in einer Region außerhalb der Region verarbeitet, in der diese Instanz erstellt wurde.

## <a name="obtain-an-update-for-your-devices"></a>Abrufen eines Updates für Ihre Geräte

Nachdem Sie Device Update eingerichtet und Ihre Geräte bereitgestellt haben, benötigen Sie die Updatedatei(en), die Sie auf diesen Geräten bereitstellen möchten.

Wenn Sie Geräte von einem OEM oder Lösungsintegrator erworben haben, stellt die entsprechende Organisation höchstwahrscheinlich Updatedateien für Sie bereit, ohne dass Sie die Updates selbst erstellen müssen. Wenden Sie sich an den OEM oder Lösungsintegrator, um zu erfahren, wie Updates verfügbar gemacht werden.

Falls Ihre Organisation bereits Software für die von Ihnen verwendeten Geräte erstellt, werden die Updates für diese Software von der zuständigen Gruppe erstellt. Beginnen Sie beim Erstellen eines Updates mit Device Update for IoT Hub je nach Szenario entweder [mit dem imagebasierten oder mit dem paketbasierten Ansatz](understand-device-update.md#support-for-a-wide-range-of-update-artifacts). Hinweis: Wenn Sie Ihre eigenen Updates erstellen möchten, aber gerade erst am Anfang stehen, ist GitHub eine hervorragende Option für die Entwicklungsverwaltung. Sie können dort Ihren Quellcode speichern und verwalten sowie CI- und CD-Vorgänge (Continuous Integration und Continuous Deployment) mithilfe von [GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration) durchführen.

## <a name="create-a-device-update-import-manifest"></a>Erstellen eines Device Update-Importmanifests

Falls Sie dies noch nicht getan haben, machen Sie sich mit den grundlegenden [Importkonzepten](import-concepts.md) vertraut, und probieren Sie zunächst ein [imagebasiertes](device-update-raspberry-pi.md) oder [paketbasiertes](device-update-ubuntu-agent.md) Tutorial aus.

1. Stellen Sie sicher, dass sich Ihre Updatedateien in einem für PowerShell zugänglichen Verzeichnis befinden.

2. Erstellen Sie eine Textdatei namens **AduUpdate.psm1** im Verzeichnis, indem sich Ihre Updateimagedatei oder die APT-Manifestdatei befinden. Öffnen Sie dann das PowerShell-Cmdlet [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets), kopieren Sie den Inhalt in Ihre Textdatei, und speichern Sie dann die Textdatei.

3. Navigieren Sie in PowerShell zum Verzeichnis, indem Sie in Schritt 2 Ihr PowerShell-Cmdlet erstellt haben. Verwenden Sie die Option "Kopieren" unten und fügen Sie es dann in PowerShell ein, um die Befehle auszuführen:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Führen Sie die folgenden Befehle aus, indem Sie die Beispielpameterwerte ersetzen, um ein Importmanifest, d. h. eine JSON-Datei zur Beschreibung des Updates, zu generieren:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Die folgende Tabelle ist eine Kurzreferenz zum Auffüllen der oben genannten Parameter. Sie können sich auch das vollständige [Importmanifestschema](import-schema.md) ansehen, um weitere Informationen zu erhalten.

    | Parameter | BESCHREIBUNG |
    | --------- | ----------- |
    | deviceManufacturer | Hierbei handelt es sich um den Hersteller des Geräts, mit dem das Update kompatibel ist, z. B. Contoso. Dieser Parameter muss mit der _manufacturer_-[Geräteeigenschaft](./device-update-plug-and-play.md#device-properties) übereinstimmen.
    | deviceModel | Modell des Geräts, mit dem das Update kompatibel ist, z. B. „Toaster“. Dieser Parameter muss mit der _model_-[Geräteeigenschaft](./device-update-plug-and-play.md#device-properties) übereinstimmen.
    | updateProvider | Bei diesem Parameter handelt es sich um die Entität, die das Update erstellt oder verantwortlich für dieses ist. Oft ist dies ein Firmenname.
    | updateName | Hierbei handelt es sich um einen Bezeichner für eine Klasse von Updates. Die Klasse kann etwas von Ihnen Gewähltes sein. Oft ist es ein Geräte- oder Modellname.
    | updateVersion | Hierbei handelt es sich um die Versionsnummer, die dieses Update von anderen mit denselben Anbietern und Namen unterscheidet. Der Parameter entspricht nicht der Version einer einzelnen Softwarekomponente auf dem Gerät (kann jedoch ausgewählt werden).
    | updateType | <ul><li>`microsoft/swupdate:1` für Imageupdate angeben</li><li>`microsoft/apt:1` für Paketupdate angeben</li></ul>
    | installedCriteria | Wird während der Bereitstellung verwendet, um die bereits auf dem Gerät installierte Version mit der Version des Updates zu vergleichen. Wenn das Update auf dem Gerät bereitgestellt wird, wird ein Fehler zurückgegeben, wenn der installedCriteria-Wert nicht mit der Version auf dem Gerät übereinstimmt.<ul><li>Geben Sie für den `microsoft/swupdate:1`-Updatetyp den Wert von SWVersion an. </li><li>Geben Sie für den `microsoft/apt:1`-Updatetyp **Name-Version** an, wobei _Name_ der Name des APT-Manifests und _Version_ die Version des APT-Manifests ist. Zum Beispiel: contoso-iot-edge-1.0.0.0.
    | updateFilePath(s) | Pfad zu der/den Updatedatei(en) auf Ihrem Computer.


## <a name="review-the-generated-import-manifest"></a>Überprüfen des generierten Importmanifests

Ein Beispiel einer Manifestausgabe sehen Sie unten. In diesem Beispiel gibt es zwei Dateien, die dieses Update enthalten: eine JSON-Datei und eine ZIP-Datei. Wenn Sie Fragen zu einem der Elemente haben, sehen Sie sich das vollständige [Importmanifestschema](import-schema.md) an. 
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-an-update"></a>Importieren eines Updates

> [!NOTE]
> Die folgenden Anweisungen zeigen, wie Sie ein Update über die Benutzeroberfläche des Azure-Portals importieren. Sie können stattdessen auch das [Geräteupdate für IoT Hub-APIs](#if-youre-importing-via-apis-instead) verwenden, um ein Update zu importieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer IoT Hub-Instanz mit Device Update.

2. Wählen Sie links auf der Seite unter „Automatische Geräteverwaltung“ die Option „Geräteupdates“ aus.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importieren von Updates" lightbox="media/import-update/import-updates-3.png":::

3. Oben auf dem Bildschirm werden mehrere Registerkarten angezeigt. Wählen Sie die Registerkarte „Updates“ aus.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Updates" lightbox="media/import-update/updates-tab.png":::

4. Wählen Sie unter der Überschrift „Ready to Deploy“ (Bereit für die Bereitstellung) die Option „+ Import New Update“ (+ Neues Update importieren) aus.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Neues Update importieren" lightbox="media/import-update/import-new-update-2.png":::

5. Wählen Sie das Ordnersymbol oder das Textfeld unter „Select an Import Manifest File“ (Importmanifestdatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie das Importmanifest aus, das Sie zuvor mithilfe des PowerShell-Cmdlets erstellt haben. Wählen Sie als Nächstes das Ordnersymbol oder das Textfeld unter „Select one or more update files“ (Mindestens eine Updatedatei auswählen) aus. Daraufhin wird ein Dialogfeld für Dateiauswahl angezeigt. Wählen Sie dieselben Updatedatei(en) aus, die Sie beim Erstellen des Importmanifests eingeschlossen haben.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Updatedateien auswählen" lightbox="media/import-update/select-update-files.png":::

6. Wählen Sie unter „Select a storage container“ (Speichercontainer auswählen) das Ordnersymbol oder das Textfeld aus. Wählen Sie dann das entsprechende Speicherkonto aus. Der Speichercontainer wird zum vorübergehenden Stagen der Updatedateien verwendet.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Speicherkonto" lightbox="media/import-update/storage-account.png":::

7. Wenn Sie bereits einen Container erstellt haben, können Sie ihn wiederverwenden. (Wählen Sie andernfalls „+ Container“ aus, um einen neuen Speichercontainer für Updates zu erstellen.)  Wählen Sie den Container aus, den Sie verwenden möchten, und klicken Sie auf „Select“ (Auswählen).

   :::image type="content" source="media/import-update/container.png" alt-text="„Container“ auswählen" lightbox="media/import-update/container.png":::

8. Wählen Sie „Submit“ (Übermitteln) aus, um den Importvorgang zu starten.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Update veröffentlichen" lightbox="media/import-update/publish-update.png":::

9. Der Importvorgang beginnt, und auf dem Bildschirm wird der Abschnitt „Import History“ (Importverlauf) angezeigt. Wählen Sie „Refresh“ (Aktualisieren) aus, um den Fortschritt anzuzeigen, bis der Importvorgang abgeschlossen ist (je nach Größe des Updates kann dieser Vorgang innerhalb weniger Minuten abgeschlossen sein, könnte aber auch länger dauern).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Updateimportsequenzierung" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Wenn in der Spalte „Status“ angezeigt wird, dass der Import erfolgreich war, wählen Sie den Header „Ready to Deploy“ (Bereit für die Bereitstellung) aus. Ihr importiertes Update sollte jetzt in der Liste angezeigt werden.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Auftragsstatus" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Nächste Schritte

[Erstellen Sie Gruppen.](create-update-group.md)

[Informieren Sie sich über Konzepte zum Importieren.](import-concepts.md)

## <a name="if-youre-importing-via-apis-instead"></a>Wenn Sie stattdessen über APIs importieren

Beachten Sie Folgendes, wenn Sie ein Update mit dem [Geräteupdate für IoT Hub-Update-APIs](/rest/api/deviceupdate/updates) anstatt über das Azure-Portal importieren möchten:
  - Sie müssen Ihre Updatedatei(en) in einen Azure Blob Storage-Speicherort hochladen, bevor Sie die Update-APIs aufrufen.
  - Sie können diesen [Beispiel-API-Aufruf](import-schema.md#example-import-request-body), der das oben von Ihnen erstellte Importmanifest verwendet, als Referenz nutzen.
  - Wenn Sie beim Testen dieselbe SAS-URL erneut verwenden, treten möglicherweise Fehler auf, wenn das Token abläuft. Dies ist der Fall, wenn Sie das Importmanifest sowie den Updateinhalt selbst übermitteln.