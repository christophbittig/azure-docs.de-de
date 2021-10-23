---
title: Tutorial zum Exportieren von Daten aus Azure Blob-Speicher mit Azure Import/Export | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Exportaufträge im Azure-Portal erstellen und Daten aus Azure-Blobs übertragen.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: ccda14f4046efe32370b206577807dca558d9f25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709652"
---
# <a name="tutorial-export-data-from-azure-blob-storage-with-azure-importexport"></a>Tutorial: Exportieren von Daten aus Azure Blob-Speicher mit Azure Import/Export

Dieser Artikel enthält ausführliche Anweisungen zum Verwenden des Azure Import/Export-Diensts, um große Datenmengen sicher aus Azure Blob Storage zu exportieren. Für diesen Dienst müssen Sie leere Laufwerke an das Azure-Rechenzentrum senden. Der Dienst exportiert Daten aus Ihrem Speicherkonto auf die Laufwerke und versendet diese dann zurück.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Voraussetzungen zum Exportieren von Daten aus Azure Blob-Speicher mit Azure Import/Export
> * Schritt 1: Erstellen eines Exportauftrags
> * Schritt 2: Versenden der Laufwerke
> * Schritt 3: Aktualisieren des Auftrags mit Nachverfolgungsinformationen
> * Schritt 4: Empfangen der Datenträger
> * Schritt 5: Entsperren der Datenträger

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines Exportauftrags zum Übertragen von Daten aus Azure Blob Storage überprüfen Sie sorgfältig anhand der folgenden Liste, ob alle Voraussetzungen für diesen Dienst erfüllt sind.
Die Voraussetzungen lauten wie folgt:

- Ein aktives Azure-Abonnement, das für den Import/Export-Dienst verwendet werden kann
- Mindestens ein Azure Storage-Konto Hier finden Sie die Liste der [für den Import/Export-Dienst unterstützten Speicherkonten und Speichertypen](storage-import-export-requirements.md). Weitere Informationen zum Erstellen eines neuen Speicherkontos finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).
- Eine angemessene Anzahl von Datenträgern der [unterstützten Typen](storage-import-export-requirements.md#supported-disks) Sie können mithilfe des Azure Import/Export-Tools bestimmen, wie viele Datenträger bereitgestellt werden sollen. Die entsprechenden Schritte finden Sie unter [Bestimmen der Anzahl benötigter Laufwerke](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need).
- Sie benötigen ein FedEx/DHL-Konto. Wenn Sie einen anderen Spediteur als FedEx/DHL verwenden möchten, wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team.
  - Das Konto muss gültig sein, es muss Guthaben vorhanden sein und es muss der Rückversand aktiviert sein.
  - Generieren Sie eine Nachverfolgungsnummer für den Exportauftrag.
  - Jeder Auftrag benötigt eine separate Nachverfolgungsnummer. Mehrere Aufträge mit derselben Nachverfolgungsnummer werden nicht unterstützt.
  - Wenn Sie kein Spediteurskonto haben, wechseln Sie zu:
    - [Erstellen eines FedEx-Kontos](https://www.fedex.com/en-us/create-account.html) oder
    - [Erstellen eines DHL-Kontos](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Schritt 1: Erstellen eines Exportauftrags

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um im Azure-Portal einen Exportauftrag zu erstellen.

1. Melden Sie sich bei <https://portal.azure.com/> an.
2. Suchen Sie nach **Aufträge importieren/exportieren**.

    ![Suchen nach „Aufträge importieren/exportieren“](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Wählen Sie **+ Neu** aus.

    ![Auswählen von „+ Neu“, um einen neuen Auftrag zu erstellen ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. Gehen Sie unter **Grundlegende Einstellungen** wie folgt vor:

   1. Wählen Sie ein Abonnement aus.
   1. Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und erstellen Sie eine neue.
   1. Geben Sie einen aussagekräftigen Namen für den Importauftrag ein. Verwenden Sie den Namen, um den Fortschritt Ihrer Aufträge zu verfolgen.
       * Der Name darf nur Kleinbuchstaben, Ziffern und Bindestriche enthalten.
       * Der Name muss mit einem Buchstaben beginnen und darf keine Leerzeichen enthalten.

   1. Wählen Sie **Export aus Azure** aus.

    ![Grundlegende Optionen für einen Exportauftrag](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Wählen Sie **Weiter: Auftragsdetails >** aus, um fortzufahren.

5. Gehen Sie unter **Auftragsdetails** wie folgt vor:

   1. Wählen Sie die Azure-Region aus, in der sich Ihre Daten zurzeit befinden.
   1. Wählen Sie das Speicherkonto aus, aus dem Sie Daten exportieren möchten. Verwenden Sie ein Speicherkonto in der Nähe Ihres Standorts.

      Der Abgabeort wird automatisch basierend auf der Region des ausgewählten Speicherkontos aufgefüllt.

   1. Geben Sie die Blobdaten an, die aus Ihrem Speicherkonto auf Ihr leeres Laufwerk bzw. auf Ihre leeren Laufwerke exportieren möchten. Wählen Sie eine der drei folgenden Methoden:

      - Wählen Sie für die Blobdaten im Speicherkonto **Alle exportieren** aus.

        ![Alle exportieren](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Wählen Sie **Ausgewählte Container und Blobs** aus, und geben Sie zu exportierende Container und Blobs an. Sie können mehrere Auswahlmethoden verwenden. Wenn Sie die Option **Hinzufügen** auswählen, wird auf der rechten Seite ein Bereich geöffnet, in dem Sie Ihre Auswahlzeichenfolgen hinzufügen können.

        |Option|BESCHREIBUNG|
        |------|-----------|      
        |**Container hinzufügen**|Bei dieser Option werden alle Blobs in einem Container exportiert.<br>Wählen Sie **Container hinzufügen** aus, und geben Sie die einzelnen Containernamen ein.|
        |**Blobs hinzufügen**|Bei dieser Option können Sie einzelne zu exportierende Blobs angeben.<br>Wählen Sie **Blobs hinzufügen** aus. Geben Sie anschließend den relativen Pfad des Blobs an (beginnend mit dem Containernamen). Verwenden Sie *$root* , um den Stammcontainer festzulegen.<br>Sie müssen die Blobpfade in einem gültigen Format angeben, um Fehler während der Verarbeitung zu vermeiden. Dies ist in diesem Screenshot dargestellt. Weitere Informationen finden Sie unter [Beispiele für gültige Blobpfade](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).|
        |**Präfixe hinzufügen**|Verwenden Sie ein Präfix, um eine Gruppe ähnlich benannter Container oder ähnlich benannter Blobs in einem Container auszuwählen. Bei dem Präfix kann es sich um das Präfix des Containernamens, den vollständigen Containernamen oder einen vollständigen Containernamen gefolgt vom Präfix des Blobnamens handeln. |

        ![Exportieren ausgewählter Container und Blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Wählen Sie **Aus Bloblistendatei exportieren (XML-Format)** und anschließend eine XML-Datei aus, die eine Liste mit Pfaden und Präfixen für die Blobs enthält, die aus dem Speicherkonto exportiert werden sollen. Die XML-Datei muss erstellt und in einem Container für das Speicherkonto gespeichert werden. Die Datei darf nicht leer sein.

      > [!IMPORTANT]
      > Wenn Sie eine XML-Datei verwenden, um die zu exportierenden Blobs auszuwählen, achten Sie darauf, dass der XML-Code gültige Pfade und/oder Präfixe enthält. Wenn die Datei ungültig ist oder keine Daten mit den angegebenen Pfaden übereinstimmen, wird der Auftrag mit partiellen Daten oder ohne exportierte Daten beendet.

       Informationen zum Hinzufügen einer XML-Datei zu einem Container finden Sie unter [Exportauftrag mit XML-Datei](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Exportieren aus der Bloblistendatei](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Wenn ein zu exportierendes Blob während des Kopierens der Daten verwendet wird, erstellt der Azure Import/Export-Dienst eine Momentaufnahme des Blobs und kopiert die Momentaufnahme.

   Wählen Sie **Weiter: Versand >** aus, um fortzufahren.

6. In **Versand**:

    - Wählen Sie den Spediteur in der Dropdownliste aus. Wenn Sie einen anderen Spediteur als FedEx/DHL beauftragen möchten, wählen Sie eine der Optionen in der Dropdownliste aus. Wenden Sie sich unter `adbops@microsoft.com` an das Azure Data Box Operations-Team, und informieren Sie es über den von Ihnen vorgesehenen Spediteur.
    - Geben Sie eine gültige Spediteurkontonummer ein, die Sie mit diesem Spediteur erstellt haben. Microsoft verwendet dieses Konto, um die Laufwerke nach Abschluss des Exportauftrags an Sie zurückzuschicken.
    - Geben Sie vollständige und gültige Kontaktdaten an: Name, Telefonnummer, E-Mail-Adresse, Straße, Stadt, PLZ, Bundesstaat/Provinz und Land/Region.

        > [!TIP]
        > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

    Wählen Sie **Überprüfen und erstellen** aus, um den Vorgang fortzusetzen.

7. Gehen Sie unter **Überprüfen und erstellen** wie folgt vor:

   1. Überprüfen Sie die Details zum Auftrag.
   1. Notieren Sie den Auftragsnamen und die angegebene Lieferadresse des Azure-Rechenzentrums für das Senden der Datenträger an Azure.

      > [!NOTE]
      > Senden Sie die Datenträger immer an das im Azure-Portal angegebene Rechenzentrum. Wenn die Datenträger an das falsche Rechenzentrum versendet werden, wird der Auftrag nicht verarbeitet.

   1. Machen Sie sich mit den **Bedingungen** in puncto Datenschutz und Quelldatenlöschung für Ihren Auftrag vertraut. Wenn Sie mit den Bedingungen einverstanden sind, aktivieren Sie das Kontrollkästchen daneben. Der Auftrag wird überprüft.

   ![Überprüfen und Erstellen des Exportauftrags](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Wenn die Überprüfung erfolgreich war, wählen Sie **Erstellen** aus.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie die folgenden Schritte aus, um im Azure-Portal einen Exportauftrag zu erstellen.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Erstellen eines Auftrags

1. Verwenden Sie den Befehl [az extension add](/cli/azure/extension#az_extension_add), um die Erweiterung [az import-export](/cli/azure/import-export) hinzuzufügen:

    ```azurecli
    az extension add --name import-export
    ```

1. Um eine Liste der Standorte abzurufen, von denen Sie Datenträger erhalten können, verwenden Sie den Befehl [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list):

    ```azurecli
    az import-export location list
    ```

1. Führen Sie wie folgt den Befehl [az import-export create](/cli/azure/import-export#az_import_export_create) aus, um einen Exportauftrag zu erstellen, der Ihr vorhandenes Speicherkonto verwendet:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

   Dieser Auftrag exportiert alle Blobs in Ihrem Speicherkonto. Sie können ein Blob für den Export angeben, indem Sie **--export** durch diesen Wert ersetzen:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Dieser Parameterwert exportiert das Blob mit dem Namen *logo.bmp* im Stammcontainer.

   Sie haben auch die Möglichkeit, mithilfe eines Präfix alle Blobs in einem Container auszuwählen. Ersetzen Sie **--export** durch diesen Wert:

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Weitere Informationen finden Sie unter [Beispiele für gültige Blobpfade](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Wenn das zu exportierende Blob während des Kopierens der Daten verwendet wird, erstellt der Azure Import/Export-Dienst eine Momentaufnahme des Blobs und kopiert die Momentaufnahme.

1. Verwenden Sie den Befehl [az import-export list](/cli/azure/import-export#az_import_export_list), um alle Aufträge für die Ressourcengruppe „myierg“ anzuzeigen:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Um Ihren Auftrag zu aktualisieren oder abzubrechen, führen Sie den Befehl [az import-export update](/cli/azure/import-export#az_import_export_update) aus:

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Führen Sie die folgenden Schritte aus, um einen Exportauftrag in Azure PowerShell zu erstellen.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.ImportExport** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Erstellen eines Auftrags

1. Um eine Liste der Standorte abzurufen, von denen Sie Datenträger erhalten können, verwenden Sie das Cmdlet [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation):

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Führen Sie das folgende Beispiel [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) aus, um einen Exportauftrag zu erstellen, der Ihr vorhandenes Speicherkonto verwendet:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Geben Sie anstelle einer E-Mail-Adresse für einen einzelnen Benutzer, eine Gruppen E-Mail-Adresse ein. Dadurch wird sichergestellt, dass Sie Benachrichtigungen erhalten, selbst wenn ein Administrator geht.

   Dieser Auftrag exportiert alle Blobs in Ihrem Speicherkonto. Sie können ein Blob für den Export angeben, indem Sie **-ExportBlobListblobPath** durch diesen Wert ersetzen:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Dieser Parameterwert exportiert das Blob mit dem Namen *logo.bmp* im Stammcontainer.

   Sie haben auch die Möglichkeit, mithilfe eines Präfix alle Blobs in einem Container auszuwählen. Ersetzen Sie **-ExportBlobListblobPath** durch diesen Wert:

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Weitere Informationen finden Sie unter [Beispiele für gültige Blobpfade](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths).

   > [!NOTE]
   > Wenn das zu exportierende Blob während des Kopierens der Daten verwendet wird, erstellt der Azure Import/Export-Dienst eine Momentaufnahme des Blobs und kopiert die Momentaufnahme.

1. Verwenden Sie das Cmdlet [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport), um alle Aufträge für die Ressourcengruppe „myierg“ anzuzeigen:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Um Ihren Auftrag zu aktualisieren oder abzubrechen, führen Sie das Cmdlet [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) aus:

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-2-ship-the-drives"></a>Schritt 2: Versenden der Laufwerke

Wenn Sie die Anzahl der benötigten Laufwerke nicht kennen, finden Sie weitere Informationen unter [Bestimmen der Anzahl benötigter Laufwerke](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need). Wenn Sie die Anzahl der Laufwerke kennen, fahren Sie mit dem Versenden der Laufwerke fort.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Schritt 3: Aktualisieren des Auftrags mit Nachverfolgungsinformationen

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Schritt 4: Empfangen der Datenträger

Wenn im Dashboard angegeben wird, dass der Auftrag abgeschlossen wurde, werden die Laufwerke an Sie versendet, und die Nachverfolgungsnummer für die Lieferung ist im Portal verfügbar.

1. Wenn Sie die Laufwerke mit exportierten Daten erhalten haben, benötigen Sie die BitLocker-Schlüssel, um die Laufwerke zu entsperren. Wechseln Sie im Azure-Portal zum Exportauftrag. Klicken auf die Registerkarte **Import/Export**.
2. Wählen Sie Ihren Exportauftrag in der Liste aus, und klicken Sie darauf. Wechseln Sie zu **Verschlüsselung**, und kopieren Sie die Schlüssel.

   ![BitLocker-Schlüssel für einen Exportauftrag anzeigen](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Verwenden Sie die BitLocker-Schlüssel zum Entsperren der Datenträger.

Der Export ist abgeschlossen.

## <a name="step-5-unlock-the-disks"></a>Schritt 5: Entsperren der Datenträger

Geben Sie den folgenden Befehl ein, um das Laufwerk zu entsperren:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Hier sehen Sie eine Beispieleingabe.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Zu diesem Zeitpunkt können Sie den Auftrag löschen oder belassen. Aufträge werden nach 90 Tagen automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen von Auftrags- und Laufwerkstatus](storage-import-export-view-drive-status.md)
* [Überprüfen der Kopierprotokolle für Import/Export](storage-import-export-tool-reviewing-job-status-v1.md)
