---
title: Tutorial zum Übertragen von Daten in Azure Files mit Azure Import/Export | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Import- und Exportaufträge im Azure-Portal erstellen, um Daten in Azure Files zu übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/06/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 4f8d984d97c046891008c1e1e3904ef065198f98
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709590"
---
# <a name="tutorial-transfer-data-to-azure-files-with-azure-importexport"></a>Tutorial: Übertragen von Daten in Azure Files mit Azure Import/Export

Dieser Artikel enthält schrittweise Anweisungen zur Verwendung des Azure Import/Export-Diensts, um große Datenmengen sicher in Azure Files zu importieren. Zum Importieren von Daten erfordert der Dienst, dass Sie unterstützte Datenträger versenden, die die Daten enthalten, die im Azure-Rechenzentrum gespeichert werden sollen.

Der Import/Export-Dienst unterstützt nur den Import von Azure Files zu Azure Storage. Das Exportieren von Azure Files wird nicht unterstützt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen für den Import von Daten in Azure Files
> * Schritt 1: Vorbereiten der Laufwerke
> * Schritt 2: Erstellen eines Importauftrags
> * Schritt 3: Versenden der Laufwerke an das Azure-Rechenzentrum
> * Schritt 4: Aktualisieren des Auftrags mit Nachverfolgungsinformationen
> * Schritt 5: Überprüfen des Datenuploads in Azure

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Importauftrags zum Übertragen von Daten in Azure Files überprüfen Sie sorgfältig die folgende Liste der Voraussetzungen, und führen Sie sie aus. Die Voraussetzungen lauten wie folgt:

- Ein aktives Azure-Abonnement zur Verwendung mit dem Import/Export-Dienst.
- Mindestens ein Azure Storage-Konto Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md).
  - Konfigurieren Sie ggf. große Dateifreigaben im Speicherkonto. Wenn eine Dateifreigabe während der Importvorgänge in Azure Files nicht über genügend freien Speicherplatz verfügt, wird die automatische Aufteilung der Daten auf mehrere Azure-Dateifreigaben nicht mehr unterstützt, und beim Kopiervorgang tritt ein Fehler auf. Anweisungen finden Sie unter [Aktivieren großer Dateifreigaben für ein vorhandenes Konto](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#enable-large-files-shares-on-an-existing-account).
  - Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).
- Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks)
- Ein Windows-System, auf dem eine [unterstützte Betriebssystemversion](storage-import-export-requirements.md#supported-operating-systems) ausgeführt wird
- Laden Sie das aktuelle Release von Version 2 des Azure Import/Export-Tools für Dateien auf das Windows-System herunter:
  1. [Herunterladen der Version 2 von WAImportExport.](https://aka.ms/waiev2) Die aktuelle Version ist 2.2.0.300.
  1. Entzippen Sie die Dateien in den Standardordner `WaImportExportV2`. Beispiel: `C:\WaImportExportV2`.
- Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team.
    - Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
    - Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
    - Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt.
    - Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
        - [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder
        - [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>Schritt 1: Vorbereiten der Laufwerke

Dieser Schritt generiert eine Journaldatei. In der Journaldatei werden grundlegende Informationen wie Laufwerksseriennummer, Verschlüsselungsschlüssel und Speicherkontendetails gespeichert.

Führen Sie zum Vorbereiten der Laufwerke die folgenden Schritte aus.

1. Stellen Sie die Verbindung Ihrer Laufwerke mit dem Windows-System über SATA-Anschlüsse her.
2. Erstellen Sie ein einzelnes NTFS-Volume auf jedem Laufwerk. Weisen Sie dem Volume einen Laufwerkbuchstaben zu. Verwenden Sie keine Bereitstellungspunkte.
3. Ändern Sie die Datei *dataset.csv* im Stammverzeichnis, in dem sich das Tool befindet. Je nachdem, ob Sie eine Datei, einen Ordner oder beides importieren möchten, fügen Sie der *dataset.csv*-Datei Einträge wie in den folgenden Beispielen hinzu.

   - **So importieren Sie eine Datei:** Im folgenden Beispiel befinden sich die zu kopierenden Daten auf dem Laufwerk „F:“. Die Datei *MyFile1.txt* wird auf das Stammverzeichnis von *MyAzureFileshare1* kopiert. Wenn *MyAzureFileshare1* nicht vorhanden ist, wird es im Azure Storage-Konto erstellt. Die Ordnerstruktur wird beibehalten.

       ```
           BasePath,DstItemPathOrPrefix,ItemType
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file
       ```

   - **So importieren Sie einen Ordner**: Alle Dateien und Ordner in *MyFolder2* werden rekursiv in die Dateifreigabe kopiert. Die Ordnerstruktur wird beibehalten. Wenn Sie eine Datei mit dem gleichen Namen wie eine vorhandene Datei im Zielordner importieren, überschreibt die importierte Datei diese Datei.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file
       ```
   
       > [!NOTE]
       > Der Parameter „/Disposition“, mit dem Sie festlegen können, was beim Importieren einer Datei geschehen soll, die bereits in früheren Versionen des Tools vorhanden ist, wird in Version 2.2.0.300 von Azure Import/Export nicht unterstützt. In früheren Toolversionen wurde eine importierte Datei, die den gleichen Namen wie eine vorhandene Datei hat, standardmäßig umbenannt.

     Mehrere Einträge können gemäß importierter Ordner oder Dateien in derselben Datei vorgenommen werden.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file
           "F:\MyFolder2\","MyAzureFileshare1/",file
       ```

<!--ARCHIVED ARTICLE -Learn more about [preparing the dataset CSV file](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).-->


4. Ändern Sie die Datei *driveset.csv* im Stammverzeichnis, in dem sich das Tool befindet. Fügen Sie Einträge in der Datei *driveset.csv* wie in den folgenden Beispielen hinzu. Die Driveset-Datei enthält die Liste der Datenträger und die entsprechenden Laufwerkbuchstaben, damit das Tool die Liste der vorzubereitenden Datenträger richtig auswählen kann.

    In diesem Beispiel wird vorausgesetzt, dass zwei Festplatten angeschlossen sind und die grundlegenden NTFS-Volumes G:\ und H:\ erstellt werden. H:\ wird nicht verschlüsselt, während G:\ bereits verschlüsselt ist. Das Tool formatiert und verschlüsselt nur den Datenträger, der H:\ hostet (und nicht G:\).

   - **Für einen Datenträger, die nicht verschlüsselt ist**: Geben Sie *Encrypt* zum Aktivieren der BitLocker-Verschlüsselung auf dem Datenträger an.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Für einen Datenträger, der bereits verschlüsselt ist**: Geben Sie *AlreadyEncrypted* und den BitLocker-Schlüssel an.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Mehrere Einträge können gemäß mehrerer Laufwerke in derselben Datei vorgenommen werden. Erfahren Sie mehr über das [Vorbereiten der Driveset-CSV-Datei](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Verwenden der Option `PrepImport` zum Kopieren und Vorbereiten von Daten auf dem Datenträger. Führen Sie für die erste Kopiersitzung zum Kopieren von Verzeichnissen und/oder Dateien mit einer neuen Kopiersitzung folgenden Befehl aus:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Ein Importbeispiel ist nachfolgend dargestellt.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Für jede Ausführung der Befehlszeile wird eine Journaldatei mit dem von Ihnen angegebenen Namen mit Parameter `/j:` erstellt. Jedes Laufwerk, das Sie vorbereiten, hat eine Journaldatei, die hochgeladen werden muss, wenn Sie den Importauftrag erstellen. Laufwerke ohne Journaldateien werden nicht verarbeitet.

    > [!IMPORTANT]
    > Ändern Sie nach Abschluss der Datenträgervorbereitung weder die Journaldateien noch die Daten auf den Datenträgerlaufwerken, und formatieren Sie keine Datenträger neu.

Zusätzliche Beispiele finden Sie unter [Beispiele für Journaldateien](#samples-for-journal-files).

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

   1. Laden Sie die Journaldateien hoch, die Sie im vorhergehenden [Schritt 1: Vorbereiten der Laufwerke](#step-1-prepare-the-drives) erstellt haben.
   1. Wählen Sie die Azure-Zielregion für die Bestellung aus.
   1. Wählen Sie das Speicherkonto für den Import aus.

      Der Ablageort wird automatisch basierend auf der Region des ausgewählten Speicherkontos mit Daten aufgefüllt.

   1. Wenn Sie kein ausführliches Protokoll speichern möchten, deaktivieren Sie die Option **Ausführliches Protokoll im Blobcontainer „waimportexport“ speichern**.


   ![Importauftrag erstellen – Schritt 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   Wählen Sie **Weiter: Versand >** aus, um fortzufahren.

6. In **Versand**:

    1. Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
    1. Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Importauftrags an Sie zurückzuschicken.
    1. Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region.

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
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
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

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Schritt 3: Versenden der Laufwerke an das Azure-Rechenzentrum

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Schritt 4: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Schritt 5: Überprüfen des Datenuploads in Azure

Überwachen Sie den Auftrag bis zu seinem Abschluss. Sobald der Auftrag abgeschlossen ist, überprüfen Sie, ob Ihre Daten in Azure hochgeladen wurden. Überprüfen Sie Ihre Kopierprotokolle auf Fehler. Weitere Informationen finden Sie unter [Überprüfen des Status von Azure Import/Export-Aufträgen mithilfe von Kopierprotokolldateien](storage-import-export-tool-reviewing-job-status-v1.md). Löschen Sie die lokalen Daten erst, wenn Sie überprüft haben, ob die Daten erfolgreich hochgeladen wurden.

> [!NOTE]
> In der neuesten Version des Azure Import/Export-Tools für Dateien (2.2.0.300) werden die Daten nicht mehr automatisch auf mehrere Azure-Dateifreigaben aufgeteilt, wenn eine Dateifreigabe nicht über genügend freien Speicherplatz verfügt. Stattdessen tritt beim Kopiervorgang ein Fehler auf, und Sie werden vom Support kontaktiert. Sie müssen entweder große Dateifreigaben im Speicherkonto konfigurieren oder einige Daten verschieben, um Speicherplatz auf der Freigabe freizugeben. Weitere Informationen finden Sie unter [Aktivieren großer Dateifreigaben für ein vorhandenes Konto](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#enable-large-files-shares-on-an-existing-account).


## <a name="samples-for-journal-files"></a>Beispiele für Journaldateien

Um **weitere Laufwerke hinzuzufügen**, erstellen Sie eine neue Driveset-Datei, und führen Sie den Befehl wie unten beschrieben aus.

Geben Sie für nachfolgende Kopiersitzungen mit anderen Laufwerken als den in der Datei *InitialDriveset.csv* angegebenen eine neue Driveset-*CSV*-Datei an, und legen Sie sie als Wert für den Parameter `AdditionalDriveSet` fest. Verwenden Sie den **gleichen Journaldateinamen**, und geben Sie eine **neue Sitzungs-ID** an. Das Format der AdditionalDriveset-CSV-Datei ist mit dem InitialDriveSet-Format identisch.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Ein Importbeispiel ist nachfolgend dargestellt.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Um demselben Driveset zusätzliche Daten hinzuzufügen, verwenden Sie den PrepImport-Befehl für die nachfolgenden Kopiersitzungen, um zusätzliche Dateien/Verzeichnisse zu kopieren.

Geben Sie für nachfolgende Kopiersitzungen für Kopiervorgänge auf dieselben in der Datei *InitialDriveset.csv* angegebenen Festplatten den **gleichen Journaldateinamen** und eine **neue Sitzungs-ID** an. Es ist nicht erforderlich, den Speicherkontoschlüssel anzugeben.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Ein Importbeispiel ist nachfolgend dargestellt.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Kopierprotokolle für Import/Export](storage-import-export-tool-reviewing-job-status-v1.md)
