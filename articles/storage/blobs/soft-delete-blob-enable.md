---
title: Aktivieren von „Vorläufiges Löschen“ für Blobs
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie vorläufiges Löschen für Blobs aktivieren, um Blobdaten vor versehentlichem Löschen oder Überschreiben zu schützen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 790afdb013d2721bc90238cfe0caf7aa4534c632
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289498"
---
# <a name="enable-soft-delete-for-blobs"></a>Aktivieren von „Vorläufiges Löschen“ für Blobs

Vorläufiges Löschen von Blobs schützt ein einzelnes Blob sowie seine Versionen, Momentaufnahmen und Metadaten vor versehentlichen Lösch- oder Überschreibungsvorgängen, da die Daten für einen gewissen Zeitraum im System verbleiben. Während der Beibehaltungsdauer kann das Blob in dem Zustand wiederhergestellt werden, in dem es sich zum Zeitpunkt der Löschung befand. Nach Ablauf der Beibehaltungsdauer wird das Blob endgültig gelöscht. Weitere Informationen zum vorläufigen Löschen von Blobs finden Sie unter [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md).

Das vorläufige Löschen von Blobs ist Teil einer umfassenden Datenschutzstrategie für Blobdaten. Weitere Informationen zu den Datenschutzempfehlungen von Microsoft finden Sie in der [Übersicht zum Datenschutz](data-protection-overview.md).

> [!NOTE]
> Mit dem vorläufigen Löschen von Blobs lassen sich auch Blobs und Verzeichnisse in Konten schützen, für die die hierarchische Namespacefunktion aktiviert ist. Das vorläufige Löschen von Blobs für Konten, für die die hierarchische Namespacefunktion aktiviert ist, befindet sich derzeit in der öffentlichen Vorschauphase und ist global in allen Azure-Regionen verfügbar. 

Das vorläufige Löschen von Blobs ist für neue Speicherkonten standardmäßig deaktiviert. Vorläufiges Löschen kann für ein Speicherkonto jederzeit über das Azure-Portal, mithilfe von PowerShell oder über die Azure CLI aktiviert oder deaktiviert werden.

## <a name="enable-blob-soft-delete"></a>Aktivieren des vorläufigen Löschens von Blobs

### <a name="portal"></a>[Portal](#tab/azure-portal)

Über das Azure-Portal kann das vorläufige Löschen von Blobs für Ihr Speicherkonto wie folgt aktiviert werden:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto.
1. Navigieren Sie unter **Blob-Dienst** zur Option **Datenschutz**.
1. Wählen Sie im Abschnitt **Wiederherstellung** die Option **Vorläufiges Löschen für Blobs aktivieren** aus.
1. Geben Sie eine Beibehaltungsdauer zwischen einem Tag und 365 Tagen an. Microsoft empfiehlt eine Mindestbeibehaltungsdauer von sieben Tagen.
1. Speichern Sie die Änderungen.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Screenshot: Aktivieren des vorläufigen Löschens über das Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Wenn Sie das vorläufige Löschen von Blobs mithilfe von PowerShell aktivieren möchten, können Sie den Befehl [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) aufrufen und dabei die Beibehaltungsdauer in Tagen angeben.

Im folgenden Beispiel wird das vorläufige Löschen von Blobs aktiviert und die Beibehaltungsdauer auf sieben Tage festgelegt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Die aktuellen Einstellungen für das vorläufige Löschen von Blobs können mithilfe des Befehls [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) überprüft werden:

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-CLI)

Wenn Sie das vorläufige Löschen von Blobs mithilfe der Azure CLI aktivieren möchten, können Sie den Befehl [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aufrufen und dabei die Beibehaltungsdauer in Tagen angeben.

Im folgenden Beispiel wird das vorläufige Löschen von Blobs aktiviert und die Beibehaltungsdauer auf sieben Tage festgelegt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Die aktuellen Einstellungen für das vorläufige Löschen von Blobs können mithilfe des Befehls [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) überprüft werden:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>Aktivieren des vorläufigen Löschens für Blobs (hierarchischer Namespace)

Mit dem vorläufigen Löschen von Blobs lassen sich auch Blobs und Verzeichnisse in Konten schützen, für die die hierarchische Namespacefunktion aktiviert ist. 

> [!IMPORTANT]
> Das vorläufige Löschen in Konten, für die die hierarchische Namespacefunktion aktiviert ist, befindet sich derzeit in der VORSCHAUPHASE und ist global in allen Azure-Regionen verfügbar.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>
>
> Wie Sie sich für die Vorschau registrieren, erfahren Sie in [diesem Formular](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[Portal](#tab/azure-portal)

Über das Azure-Portal kann das vorläufige Löschen von Blobs für Ihr Speicherkonto wie folgt aktiviert werden:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Speicherkonto.
1. Suchen Sie unter **Datenverwaltung** die Option **Datenschutz**.
1. Wählen Sie im Abschnitt **Wiederherstellung** die Option **Vorläufiges Löschen für Blobs aktivieren** aus.
1. Geben Sie eine Beibehaltungsdauer zwischen einem Tag und 365 Tagen an. Microsoft empfiehlt eine Mindestbeibehaltungsdauer von sieben Tagen.
1. Speichern Sie die Änderungen.

> [!div class="mx-imgBorder"]
> ![Screenshot, der zeigt, wie Sie vorläufiges Löschen im Azure-Portal in Konten aktivieren, die über einen hierarchischen Namespace verfügen.](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Installieren Sie das neueste Modul **PowershellGet**. Schließen Sie die PowerShell-Konsole, und öffnen Sie sie dann erneut.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

2.  Installieren Sie das Vorschaumodul **Az.Storage**.

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```
    Weitere Informationen zum Installieren von PowerShell-Modulen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

3. Abrufen der Speicherkontoautorisierung mithilfe eines Speicherkontoschlüssels, einer Verbindungszeichenfolge oder mit Azure Active Directory (Azure AD). Siehe [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   Im folgenden Beispiel wird die Autorisierung mithilfe eines Speicherkontoschlüssels abgerufen.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. Wenn Sie das vorläufige Löschen von Blobs mithilfe von PowerShell aktivieren möchten, verwenden Sie den Befehl [Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy), und geben Sie dabei den Aufbewahrungszeitraum in Tagen an.

   Im folgenden Beispiel wird das vorläufige Löschen für ein Konto aktiviert und der Aufbewahrungszeitraum auf 4 Tage festgelegt. 

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```
5. Um die aktuellen Einstellungen für das vorläufige Löschen von Blobs zu überprüfen, verwenden Sie den Befehl `Get-AzStorageServiceProperty`:

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-CLI)

1. Öffnen Sie [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-Befehlszeilenschnittstelle lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Installieren Sie die Erweiterung `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```
3. Stellen Sie eine Verbindung mit Ihrem Speicherkonto her. Siehe [Herstellen einer Verbindung mit dem Konto](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account).

   > [!NOTE]
   > Das in diesem Artikel dargestellte Beispiel zeigt die Azure AD-Autorisierung (Azure AD). Weitere Informationen zu Autorisierungsmethoden finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure CLI](./authorize-data-operations-cli.md).
 
4. Wenn Sie das vorläufige Löschen mithilfe der Azure CLI aktivieren möchten, rufen Sie den Befehl `az storage fs service-properties update` auf, und geben Sie dabei den Aufbewahrungszeitraum in Tagen an.

   Im folgenden Beispiel wird das vorläufige Löschen von Blobs und Verzeichnissen aktiviert und der Aufbewahrungszeitraum auf 5 Tage festgelegt. 

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. Um die aktuellen Einstellungen für das vorläufige Löschen von Blobs zu überprüfen, rufen Sie den Befehl `az storage fs service-properties update` auf:

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>Nächste Schritte

- [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md)
- [Verwalten und Wiederherstellen vorläufig gelöschter Blobs](soft-delete-blob-manage.md)