---
title: 'Aktivieren des vorläufigen Löschens: Azure-Dateifreigaben'
description: Hier erfahren Sie, wie Sie das vorläufige Löschen für Azure-Dateifreigaben aktivieren, um die Wiederherstellung von Daten zu ermöglichen und versehentliches Löschen zu verhindern.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118324"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Aktivieren des vorläufigen Löschens für Azure-Dateifreigaben
Azure Files bietet vorläufiges Löschen für Dateifreigaben, damit Sie Ihre Daten leichter wiederherstellen können, wenn diese irrtümlich von einer Anwendung oder von einem anderen Benutzer des Speicherkontos gelöscht wurden. Weitere Informationen zum vorläufigen Löschen finden Sie unter [Verhindern des versehentlichen Löschens von Azure-Dateifreigaben](storage-files-prevent-file-share-deletion.md).

## <a name="applies-to"></a>Gilt für:
| Dateifreigabetyp | SMB | NFS |
|-|:-:|:-:|
| Standard-Dateifreigaben (GPv2), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Standard-Dateifreigaben (GPv2), GRS/GZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |
| Premium-Dateifreigaben (FileStorage), LRS/ZRS | ![Ja](../media/icons/yes-icon.png) | ![Nein](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Voraussetzungen
- Falls Sie Azure PowerShell verwenden möchten, [installieren Sie die neueste Version](/powershell/azure/install-az-ps).
- Falls Sie die Azure CLI verwenden möchten, [installieren Sie die neueste Version](/cli/azure/install-azure-cli).

## <a name="getting-started"></a>Erste Schritte
In den folgenden Abschnitten erfahren Sie, wie Sie vorläufiges Löschen für Azure-Dateifreigaben eines bereits vorhandenen Speicherkontos aktivieren und verwenden:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie unter **Datenspeicher** die Option **Dateifreigaben** aus.
1. Wählen Sie neben **Vorläufiges Löschen** die Option **Aktiviert** aus.
1. Wählen Sie **aktiviert** für **vorläufiges Löschen für alle Dateifreigaben** aus.
1. Wählen Sie **Aufbewahrungsdauer für Dateifreigabe in Tagen** aus, und geben Sie eine Zahl Ihrer Wahl ein.
1. Wählen Sie **Speichern** aus, um die Einstellungen für die Datenaufbewahrung zu bestätigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Screenshot: Speicherkontobereich mit den Einstellungen für vorläufiges Löschen. Hervorgehoben sind der Dateifreigabebereich, die Aktivierungsoption, das Festlegen eines Aufbewahrungszeitraums, und die Speicheroption. Dadurch wird vorläufiges Löschen für alle Dateifreigaben in Ihrem Speicherkonto aktiviert.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zum Aktivieren des vorläufigen Löschens müssen die Einstellungen für alle Azure-Dateifreigaben, auch als `FileService`-Eigenschaften bezeichnet, aktualisiert werden. Im folgenden Beispiel wird das vorläufige Löschen für alle Dateifreigaben in einem Speicherkonto aktiviert. Denken Sie daran, `<resource-group>` und `<storage-account>` durch die entsprechenden Werte für Ihre Umgebung zu ersetzen.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

Mit dem folgenden Befehl können Sie überprüfen, ob vorläufiges Löschen aktiviert ist, und die zugehörige Aufbewahrungsrichtlinie anzeigen:

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Zum Aktivieren des vorläufigen Löschens müssen die Diensteigenschaften eines Dateiclients aktualisiert werden. Im folgenden Beispiel wird das vorläufige Löschen für alle Dateifreigaben in einem Speicherkonto aktiviert. Denken Sie daran, `<resource-group>` und `<storage-account>` durch die entsprechenden Werte für Ihre Umgebung zu ersetzen.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Mit dem folgenden Befehl können Sie überprüfen, ob vorläufiges Löschen aktiviert ist, und die zugehörige Aufbewahrungsrichtlinie anzeigen:

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Wiederherstellen einer vorläufig gelöschten Dateifreigabe

# <a name="portal"></a>[Portal](#tab/azure-portal)
So stellen Sie eine vorläufig gelöschte Dateifreigabe wieder her:

1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie **Dateifreigaben** aus.
1. Aktivieren Sie auf dem Dateifreigabeblatt die Option **Gelöschte Freigaben anzeigen**, um vorläufig gelöschte Freigaben anzuzeigen.

    Dadurch werden alle Freigaben angezeigt, die sich aktuell im Zustand **Gelöscht** befinden.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Ist die Statusspalte (die Spalte neben der Namensspalte) auf „Gelöscht“ festgelegt, befindet sich die Dateifreigabe in einem vorläufig gelöschten Zustand. In diesem Fall wird sie nach Ablauf des angegebenen Aufbewahrungszeitraums endgültig gelöscht.":::

1. Wählen Sie die Freigabe und anschließend **Wiederherstellen** aus, um sie wiederherzustellen.

    Wenn die Freigabe den Status **Aktiv** hat, wurde sie wiederhergestellt.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Ist die Statusspalte (die Spalte neben der Namensspalte) auf „Aktiv“ festgelegt, wurde Ihre Dateifreigabe wiederhergestellt.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zum Wiederherstellen einer vorläufig gelöschten Dateifreigabe müssen Sie zuerst den Wert für `-DeletedShareVersion` der Freigabe abrufen. Dazu verwenden Sie den folgenden Befehl, mit dem alle gelöschten Freigaben für Ihr Speicherkonto aufgelistet werden:

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

Nachdem Sie die Freigabe ermittelt haben, die Sie wiederherstellen möchten, können Sie sie mit dem folgenden Befehl wiederherstellen:

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Zum Wiederherstellen einer vorläufig gelöschten Dateifreigabe müssen Sie zuerst den Wert für `--deleted-version` der Freigabe abrufen. Dazu verwenden Sie den folgenden Befehl, mit dem alle gelöschten Freigaben für Ihr Speicherkonto aufgelistet werden:

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

Nachdem Sie die Freigabe ermittelt haben, die Sie wiederherstellen möchten, können Sie sie mit dem folgenden Befehl wiederherstellen:

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>Deaktivieren des vorläufigen Löschens
Wenn Sie das vorläufige Löschen nicht mehr verwenden möchten, gehen Sie wie folgt vor: Wenn Sie eine Dateifreigabe dauerhaft löschen möchten, die vorläufig gelöscht wurde, müssen Sie sie wiederherstellen, das vorläufige Löschen deaktivieren und dann erneut löschen. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigieren Sie zu Ihrem Speicherkonto, und wählen Sie unter **Datenspeicher** die Option **Dateifreigaben** aus.
1. Wählen Sie den Link neben **Vorläufiges Löschen** aus.
1. Wählen Sie **deaktiviert** für **vorläufiges Löschen für alle Dateifreigaben** aus.
1. Wählen Sie **Speichern** aus, um die Einstellungen für die Datenaufbewahrung zu bestätigen.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Wenn Sie das vorläufige Löschen deaktivieren, können Sie nach Belieben alle Dateifreigaben in Ihrem Speicherkonto sofort und endgültig löschen.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Das vorläufige Löschen für Ihr Speicherkonto kann mithilfe des folgenden Befehls deaktiviert werden:

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Das vorläufige Löschen für Ihr Speicherkonto kann mithilfe des folgenden Befehls deaktiviert werden:

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>Nächste Schritte
Informationen zu einer anderen Form von Datenschutz und -wiederherstellung finden Sie in unserem Artikel [Übersicht über Freigabemomentaufnahmen für Azure Files](storage-snapshots-files.md).
