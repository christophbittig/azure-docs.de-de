---
title: Tutorial zum Importieren von Daten in Azure Blob Storage mit dem Azure Import/Export-Dienst | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Portal erstellen und Daten von und in Azure Blob Storage übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 10f2103049b47366a267fdf0412a7e3fb95a95cd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709619"
---
# <a name="tutorial-import-data-to-blob-storage-with-azure-importexport-service"></a>Tutorial: Importieren von Daten in Blob Storage mit dem Azure Import/Export-Dienst

Dieser Artikel enthält schrittweise Anweisungen zum Verwenden des Azure Import/Export-Diensts, um große Datenmengen sicher in Azure Blob Storage zu importieren. Wenn Sie Daten in Azure Blob Storage importieren möchten, müssen Sie als Dienstvoraussetzung verschlüsselte Datenträger mit den Daten an ein Azure-Rechenzentrum senden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen für den Import von Daten in Azure Blob Storage
> * Schritt 1: Vorbereiten der Laufwerke
> * Schritt 2: Erstellen eines Importauftrags
> * Schritt 3: Konfigurieren des kundenseitig verwalteten Schlüssels (optional)
> * Schritt 4: Versenden der Laufwerke
> * Schritt 5: Aktualisieren des Auftrags mit Nachverfolgungsinformationen
> * Schritt 6: Überprüfen des Datenuploads in Azure

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Importauftrags zum Übertragen von Daten in Azure Blob Storage überprüfen Sie sorgfältig die folgende Liste, ob alle Voraussetzungen für diesen Dienst erfüllt sind.
Die Voraussetzungen lauten wie folgt:

* Ein aktives Azure-Abonnement, das für den Import/Export-Dienst verwendet werden kann
* Mindestens ein Azure Storage-Konto mit einem Speichercontainer. Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md).
  * Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).
  * Informationen zum Erstellen von Speichercontainern finden Sie unter [Erstellen eines Speichercontainers](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks)
* Ein Windows-System, auf dem eine [unterstützte Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausgeführt wird
* Aktivierte BitLocker-Verschlüsselung auf dem Windows-System. Lesen Sie hierzu die [Schrittweise Anleitung zur Windows BitLocker-Laufwerkverschlüsselung](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* Laden Sie das aktuelle Release von Version 1 des Azure Import/Export-Tools für Blobs auf das Windows-System herunter:
  1. [Herunterladen der Version 1 von WAImportExport.](https://www.microsoft.com/download/details.aspx?id=42659) Die aktuelle Version ist 1.5.0.300.
  1. Entzippen Sie die Dateien in den Standardordner `WaImportExportV1`. Beispiel: `C:\WaImportExportV1`.
* Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team.
  * Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
  * Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
  * Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt.
  * Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
    * [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder
    * [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Schritt 1: Vorbereiten der Laufwerke

Dieser Schritt generiert eine Journaldatei. In der Journaldatei werden grundlegende Informationen wie Laufwerksseriennummer, Verschlüsselungsschlüssel und Speicherkontendetails gespeichert.

Führen Sie zum Vorbereiten der Laufwerke die folgenden Schritte aus.

1. Stellen Sie die Verbindung Ihrer Laufwerke mit dem Windows-System über SATA-Anschlüsse her.
2. Erstellen Sie ein einzelnes NTFS-Volume auf jedem Laufwerk. Weisen Sie dem Volume einen Laufwerkbuchstaben zu. Verwenden Sie keine Bereitstellungspunkte.
3. Aktivieren Sie die BitLocker-Verschlüsselung auf dem NTFS-Volume. Wenn Sie ein Windows Server-System verwenden, lesen Sie die Anweisungen unter [Aktivieren von BitLocker auf Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Kopieren Sie Daten in das verschlüsselte Volume. Verwenden Sie Drag & Drop oder Robocopy oder ein ähnliches Kopiertool. Eine Journaldatei (*JRN*) wird in demselben Ordner erstellt, in dem Sie das Tool ausgeführt haben.

   Wenn das Laufwerk gesperrt ist und Sie es entsperren müssen, können sich die Schritte zum Entsperren je nach Anwendungsfall unterscheiden.

   * Wenn Sie einem vorverschlüsselten Laufwerk Daten hinzugefügt haben (WAImportExport-Tool wurde nicht für die Verschlüsselung verwendet), verwenden Sie den BitLocker-Schlüssel (ein numerisches Kennwort, das Sie angeben) im Popupfenster, um das Laufwerk zu entsperren.

   * Wenn Sie einem Laufwerk, das mit dem WAImportExport-Tool verschlüsselt wurde, Daten hinzugefügt haben, verwenden Sie den folgenden Befehl, um das Laufwerk zu entsperren:

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Öffnen Sie ein PowerShell- oder Befehlszeilenfenster mit Administratorrechten. Um das Verzeichnis in den Ordner mit den entzippten Daten zu ändern, führen Sie den folgenden Befehl aus:

    `cd C:\WaImportExportV1`
6. Um den BitLocker-Schlüssel des Laufwerks abzurufen, führen Sie den folgenden Befehl aus:

    `manage-bde -protectors -get <DriveLetter>:`
7. Um den Datenträger vorzubereiten, führen Sie den folgenden Befehl aus: **Abhängig von der Datengröße kann die Vorbereitung des Datenträgers mehrere Stunden bis Tage dauern.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```
    
    Eine Journaldatei wird in demselben Ordner erstellt, in dem Sie das Tool ausgeführt haben. Es werden noch zwei weitere Dateien erstellt: eine *XML*-Datei (im Ordner, in dem Sie das Tool ausführen) und die Datei *LaufwerkManifest.xml* (im Ordner, in dem sich die Daten befinden).

    Die verwendeten Parameter werden in der folgenden Tabelle beschrieben:

    |Option  |BESCHREIBUNG  |
    |---------|---------|
    |/j:     |Der Name der Journaldatei mit der Erweiterung „.jrn“. Eine Journaldatei wird pro Laufwerk generiert. Es wird empfohlen, die Seriennummer des Datenträgers als Journaldateinamen zu verwenden.         |
    |/id:     |Die Sitzungs-ID. Verwenden Sie für jede Instanz des Befehls eine eindeutige Sitzungsnummer.      |
    |/t:     |Der Laufwerksbuchstabe des auszuliefernden Datenträgers. Beispiel: Laufwerk `D`         |
    |/bk:     |Der BitLocker-Schlüssel für das Laufwerk Sein numerisches Kennwort aus der Ausgabe von `manage-bde -protectors -get D:`      |
    |/srcdir:     |Der Laufwerksbuchstabe des auszuliefernden Datenträgers gefolgt von `:\`. Beispiel: `D:\`.         |
    |/dstdir:     |Der Name des Zielcontainers in Azure Storage.         |
    |/blobtype:     |Diese Option gibt den Typ des Blobs an, in den die Daten importiert werden sollen. Bei Blockblobs ist der Blobtyp `BlockBlob`, für Seitenblobs `PageBlob`.         |
    |/skipwrite:     | Gibt an, dass keine neuen Daten kopiert werden müssen und vorhandene Daten auf dem Datenträger vorbereitet werden sollen.          |
    |/enablecontentmd5:     |Wenn diese Option aktiviert ist, wird sichergestellt, dass MD5 berechnet und als `Content-md5`-Eigenschaft für jeden Blob festgelegt wird. Verwenden Sie diese Option nur, wenn Sie nach dem Hochladen der Daten in Azure das Feld `Content-md5` verwenden möchten. <br> Diese Option wirkt sich nicht auf die Datenintegritätsprüfung aus (diese wird standardmäßig ausgeführt). Durch diese Einstellung wird die Zeit zum Hochladen von Daten in die Cloud nicht verlängert.          |

    > [!NOTE]
    > Wenn Sie ein Blob mit dem gleichen Namen wie ein vorhandenes Blob im Zielcontainer importieren, überschreibt das importierte Blob das vorhandene Blob. In früheren Toolversionen (vor 1.5.0.300) wurde das importierte Blob standardmäßig umbenannt. Mit dem Parameter „\Disposition“ können Sie angeben, ob das Blob im Import umbenannt, überschrieben oder ignoriert werden soll.

8. Wiederholen Sie den vorherigen Schritt für jeden Datenträger, der versendet werden muss. 

   Für jede Ausführung an der Befehlszeile wird eine Journaldatei mit dem bereitgestellten Namen erstellt. 

   Zusammen mit der Journaldatei wird auch eine `<Journal file name>_DriveInfo_<Drive serial ID>.xml`-Datei erstellt, und zwar im gleichen Ordner, in dem sich das Tool befindet. Die XML-Datei wird beim Erstellen eines Auftrags anstelle der Journaldatei verwendet, wenn die Journaldatei zu groß ist.

> [!IMPORTANT]
> * Ändern Sie die Journaldateien oder die Daten auf den Laufwerken nicht, und formatieren Sie keine Festplatten neu, nachdem Sie die Vorbereitung der Festplatten abgeschlossen haben.
> * Die maximale Größe der Journaldatei, die das Portal zulässt, beträgt 2 MB. Wenn die Journaldatei diesen Grenzwert überschreitet, wird ein Fehler zurückgegeben.

## <a name="step-2-create-an-import-job"></a>Schritt 2: Erstellen eines Importauftrags

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um einen Importauftrag im Azure-Portal zu erstellen.

1. Melden Sie sich bei https://portal.azure.com/ an.
2. Suchen Sie nach **Aufträge importieren/exportieren**.

   ![Suchen nach „Aufträge importieren/exportieren“](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Wählen Sie **+ Neu** aus.

   ![Auswählen von „Neu“, um einen neuen Auftrag zu erstellen ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:

   1. Wählen Sie ein Abonnement aus.
   1. Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und erstellen Sie eine neue.
   1. Geben Sie einen aussagekräftigen Namen für den Importauftrag ein. Verwenden Sie den Namen, um den Fortschritt Ihrer Aufträge zu verfolgen.
      * Der Name darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.
      * Der Name muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten.

   1. Wählen Sie **Import in Azure** aus.

    ![Importauftrag erstellen – Schritt 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    Wählen Sie **Weiter: Auftragsdetails >** aus, um fortzufahren.

5. Gehen Sie unter **Auftragsdetails** wie folgt vor:

   1. Laden Sie die Journaldateien hoch, die Sie im vorhergehenden [Schritt 1: Vorbereiten der Laufwerke](#step-1-prepare-the-drives) erstellt haben. Bei Verwendung von `waimportexport.exe version1` müssen Sie pro vorbereitetem Laufwerk eine Datei hochladen. Wenn die Größe der Journaldatei 2 MB überschreitet, können Sie die `<Journal file name>_DriveInfo_<Drive serial ID>.xml`-Datei verwenden, die zusammen mit der Journaldatei erstellt wurde.
   1. Wählen Sie die Azure-Zielregion für die Bestellung aus.
   1. Wählen Sie das Speicherkonto für den Import aus.
      
      Der Ablageort wird automatisch basierend auf der Region des ausgewählten Speicherkontos mit Daten aufgefüllt.
   1. Wenn Sie kein ausführliches Protokoll speichern möchten, deaktivieren Sie die Option **Ausführliches Protokoll im Blobcontainer „waimportexport“ speichern**.

   ![Importauftrag erstellen – Schritt 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   Wählen Sie **Weiter: Versand >** aus, um fortzufahren.

6. In **Versand**:

   1. Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
   1. Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken. Wenn Sie keine Kontonummer haben, erstellen Sie ein [FedEx](https://www.fedex.com/us/oadr/)- oder [DHL](https://www.dhl.com/)-Spediteurkonto.
   1.  Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region.

       > [!TIP]
       > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

   ![Importauftrag erstellen – Schritt 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   Wählen Sie **Überprüfen und erstellen** aus, um den Vorgang fortzusetzen.

7. In der Bestellübersicht:

   1. Lesen Sie die **Geschäftsbedingungen**, und wählen sie dann „Ich bestätige die Richtigkeit aller angegebenen Informationen und stimme den oben genannten Bestimmungen zu“ aus. Anschließend wird die Überprüfung ausgeführt.
   1. Überprüfen Sie die in der Zusammenfassung bereitgestellten Informationen zum Auftrag. Notieren Sie sich den Namen des Auftrags und die Versandadresse des Azure-Rechenzentrums, damit Sie Datenträger an Azure zurücksenden können. Diese Informationen werden später auf dem Adressetikett verwendet.
   1. Klicken Sie auf **Erstellen**.

     ![Importauftrag erstellen – Schritt 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie die folgenden Schritte aus, um einen Importauftrag über die Azure-Befehlszeilenschnittstelle zu erstellen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Erstellen eines Auftrags

1. Verwenden Sie den Befehl [az extension add](/cli/azure/extension#az_extension_add), um die Erweiterung [az import-export](/cli/azure/import-export) hinzuzufügen:

    ```azurecli
    az extension add --name import-export
    ```

1. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen:

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Sie können ein vorhandenes Speicherkonto verwenden oder ein neues erstellen. Führen Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) aus, um ein Speicherkonto zu erstellen:

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. Um eine Liste der Standorte abzurufen, an die Sie Datenträger senden können, verwenden Sie den Befehl [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list):

    ```azurecli
    az import-export location list
    ```

1. Verwenden Sie den Befehl [az import-export location show](/cli/azure/import-export/location#az_import_export_location_show), um Standorte für Ihre Region abzurufen:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Führen Sie den folgenden Befehl [az import-export create](/cli/azure/import-export#az_import_export_create) aus, um einen Importauftrag zu erstellen:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

1. Verwenden Sie den Befehl [az import-export list](/cli/azure/import-export#az_import_export_list), um alle Aufträge für die Ressourcengruppe „myierg“ anzuzeigen:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Um Ihren Auftrag zu aktualisieren oder abzubrechen, führen Sie den Befehl [az import-export update](/cli/azure/import-export#az_import_export_update) aus:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie die folgenden Schritte aus, um einen Importauftrag in Azure PowerShell zu erstellen.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.ImportExport** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Erstellen eines Auftrags

1. Sie können eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen. Zum Erstellen einer Ressourcengruppe führen Sie das Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) aus:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Sie können ein vorhandenes Speicherkonto verwenden oder ein neues erstellen. Zum Erstellen eines Speicherkontos führen Sie das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aus:

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Um eine Liste der Standorte abzurufen, an die Sie Datenträger versenden können, verwenden Sie das Cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation):

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Verwenden Sie das Cmdlet `Get-AzImportExportLocation` mit dem Parameter `Name`, um Standorte für Ihre Region abzurufen:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Führen Sie das folgende Beispiel für [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) aus, um einen Importauftrag zu erstellen:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

1. Verwenden Sie das Cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport), um alle Aufträge für die Ressourcengruppe „myierg“ anzuzeigen:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Um Ihren Auftrag zu aktualisieren oder abzubrechen, führen Sie das Cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) aus:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>Schritt 3 (optional): Konfigurieren des kundenseitig verwalteten Schlüssels

Überspringen Sie diesen Schritt, und fahren Sie mit dem nächsten Schritt fort, wenn Sie den von Microsoft verwalteten Schlüssel zum Schutz Ihrer BitLocker-Schlüssel für die Laufwerke verwenden möchten. Wenn Sie einen eigenen Schlüssel zum Schutz des BitLocker-Schlüssels konfigurieren möchten, befolgen Sie die Anweisungen unter [Konfigurieren von kundenseitig verwalteten Schlüsseln mit Azure Key Vault für Azure Import/Export im Azure-Portal](storage-import-export-encryption-key-portal.md).

## <a name="step-4-ship-the-drives"></a>Schritt 4: Versenden der Laufwerke

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Schritt 5: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Schritt 6: Überprüfen des Datenuploads in Azure

Überwachen Sie den Auftrag bis zu seinem Abschluss. Sobald der Auftrag abgeschlossen ist, überprüfen Sie, ob Ihre Daten in Azure hochgeladen wurden. Löschen Sie die lokalen Daten erst, wenn Sie überprüft haben, ob die Daten erfolgreich hochgeladen wurden. Weitere Informationen finden Sie unter [Überprüfen des Status von Azure Import/Export-Aufträgen mithilfe von Kopierprotokolldateien](storage-import-export-tool-reviewing-job-status-v1.md).

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Kopierprotokolle für Import/Export](storage-import-export-tool-reviewing-job-status-v1.md)