---
title: Archivieren eines Blobs
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie ein Blob auf der Archivebene erstellen oder ein vorhandenes Blob auf die Archivebene verschieben.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: d83cb752eaaa2ed3edf58d3d1f31d6d7c5af76cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433135"
---
# <a name="archive-a-blob"></a>Archivieren eines Blobs

Die Archivebene ist eine Offlineebene für die Speicherung von Daten, auf die nur selten zugegriffen wird. Die Archivebene hat im Vergleich zur (heißen und kalten) Online-Ebene die niedrigsten Speicherkosten, aber dafür höhere Datenabrufkosten und eine höhere Latenz. Die Daten müssen sich mindestens 180 Tage lang auf der Archivebene befinden, sonst könnten sie frühzeitig gelöscht werden. Weitere Informationen zur Archivebene finden Sie unter [Archivzugriffsebene](access-tiers-overview.md#archive-access-tier).

Solange sich ein Blob auf der Archivebene befindet, kann es nicht gelesen oder geändert werden. Um ein Blob, das sich auf der Archivebene befindet, lesen oder herunterladen zu können, müssen Sie es zunächst auf einer Onlineebene („Heiß“ oder „Kalt“) aktivieren. Abhängig von der Priorität, die Sie für den Aktivierungsvorgang angeben, kann es bis zu 15 Stunden dauern, bis die Daten auf der Archivebene aktiviert sind. Weitere Informationen zur Blobaktivierung finden Sie unter [Übersicht über die Aktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md).

> [!CAUTION]
> Ein Blob auf der Archivebene ist &mdash; offline, d. h., es kann erst gelesen oder geändert werden, &mdash; wenn es aktiviert wurde. Der Aktivierungsprozess kann mehrere Stunden dauern und ist mit Kosten verbunden. Bevor Sie Daten auf die Archivebene verschieben, sollten Sie sich überlegen, ob sich das Offlineschalten von Blob-Daten auf Ihren Arbeitsablauf auswirken kann.

Sie können das Azure-Portal, PowerShell, Azure CLI oder eine der Azure Storage-Client-Bibliotheken verwenden, um die Datenarchivierung zu verwalten.

## <a name="archive-blobs-on-upload"></a>Archivieren von Blobs beim Hochladen

Um ein oder mehrere Blobs beim Hochladen zu archivieren, erstellen Sie das Blob direkt auf der Archivebene.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um ein Blob oder eine Gruppe von Blobs beim Hochladen aus dem Azure-Portal zu archivieren:

1. Navigieren Sie zum Zielcontainer.
1. Wählen Sie die Schaltfläche **Hochladen**.
1. Wählen Sie die Datei oder die Dateien aus, die Sie hochladen möchten.
1. Erweitern Sie den Abschnitt **Erweitert** und legen Sie die **Zugriffsebene** für das *Archiv* fest.
1. Wählen Sie die Schaltfläche **Hochladen**.

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Screenshot: Hochladen von Blobs auf eine Archivebene im Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um ein Blob oder mehrere Blobs mit PowerShell zu archivieren, rufen Sie den Befehl [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) auf, wie im folgenden Beispiel gezeigt wird. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Archive tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Archive

# Upload the contents of a sample-blobs directory to the Archive tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Archive
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um ein einzelnes Blob mit Azure CLI beim Hochzuladen zu archivieren, rufen Sie den Befehl [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) auf, wie im folgenden Beispiel gezeigt wird. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Um mehrere Blobs mit Azure CLI beim Hochzuladen zu archivieren, rufen Sie den Befehl [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) auf, wie im folgenden Beispiel gezeigt wird. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>Archivieren eines vorhandenen Blobs

Sie können ein vorhandenes Blob auf eine von zwei Arten in die Archivebene verschieben:

- Sie können die Ebene eines Blobs mit dem Vorgang [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blob-Ebene festlegen) ändern. **Blob-Ebene einstellen** verschiebt ein einzelnes Blob von einer Ebene in eine andere.

    Beachten Sie, dass Sie beim Verschieben eines Blobs in die Archivebene mit **Set Blob Tier** (Blob-Ebene festlegen) die Daten des Blobs erst lesen oder ändern können, wenn Sie das Blob wieder aktiviert haben. Wenn Sie möglicherweise die Daten des Blobs lesen oder ändern müssen, bevor das frühe Löschintervall abgelaufen ist, sollten Sie einen **Blob-Kopieren (Copy Blob)** -Vorgang durchführen, um eine Kopie des Blobs auf der Archivebene zu erstellen.

- Sie können einen Blob von einer Onlineebene mit dem Vorgang [Blob kopieren](/rest/api/storageservices/copy-blob) auf die Archivebene kopieren. Sie können den Vorgang **Blob kopieren** aufrufen, um einen Blob von einer Onlineebene (Heiß oder Kalt) auf die Archivebene zu kopieren. Der Quellblob verbleibt auf der Onlineebene und Sie können seine Daten weiterhin auf der Onlineebene lesen oder ändern.

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>Archivieren eines vorhandenen Blobs durch Ändern seiner Ebene

Verwenden Sie den Vorgang **Blob-Ebene festlegen**, um einen Blob von einer heißen oder kalten Ebene auf die Archivebene zu verschieben. Der Vorgang **Blob-Ebene festlegen** eignet sich am besten in Fällen, in denen Sie nicht auf die archivierten Daten zugreifen müssen, bevor das frühe Löschintervall abgelaufen ist.

Mit dem Vorgang **Blob-Ebene festlegen** wird die Ebene eines einzelnen Blobs geändert. Um eine Gruppe von Blobs mit optimaler Leistung auf die Archivebene zu verschieben, empfiehlt Microsoft die Durchführung eines Massenarchivierungsvorgangs. Der Massenarchivierungsvorgang sendet einen Batchbefehl **Blob-Ebene festlegen** an den Dienst in einer einzelnen Transaktion. Weitere Informationen finden Sie unter [Massenarchivierung](#bulk-archive).  

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um ein vorhandenes Blob auf die Archivebene im Azure-Portal zu verschieben:

1. Navigieren Sie zum Container des Blobs.
1. Wählen Sie das zu archivierende Blob aus.
1. Wählen Sie die Schaltfläche **Ebene ändern**.
1. Wählen Sie in der Dropdownliste **Zugriffsebene** die Option *Archiv* aus.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="Screenshot: Ändern der Blob-Ebene in das Archiv im Azure-Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Blob-Ebene mit PowerShell von „Heiß“ oder „Kalt“ auf „Archiv“ zu ändern, verwenden Sie die Eigenschaft **BlobClient** des Blobs, um einen .NET-Verweis auf den Blob zurückzusenden, und rufen Sie dann den Befehl **Zugriffsebene einstellen (SetAccessTier)** für diesen Verweis auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Archive.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Archive", $null)
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Ebene eines Blobs mit der Azure-Befehlszeilenschnittstelle von „Heiß“ oder „Kalt“ auf „Archiv“ zu ändern, rufen Sie den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>Archivieren eines vorhandenen Blobs durch einen Kopiervorgang

Verwenden Sie den Vorgang [Blob kopieren](/rest/api/storageservices/copy-blob), um einen Blob von einer heißen oder kalten Ebene auf die Archivebene zu verschieben. Der Quellblob verbleibt auf der Ebene "Heiß" oder "Kalt", während der Zielblob auf der Archivebene erstellt wird.

Der Vorgang **Blob kopieren** eignet sich am besten in Fällen, in denen Sie nicht die archivierten Daten lesen oder abändern müssen, bevor das frühe Löschintervall abgelaufen ist. Sie können auf die Daten des Quellblobs zugreifen, ohne dass Sie den archivierten Blob reaktivieren müssen.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

–

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um einen Blob mit PowerShell von einer Onlineebene auf die Archivebene zu kopieren, rufen Sie den Befehl [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) auf und geben Sie die Archivebene an. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in Hot tier with Standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Archive `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um einen Blob mit Azure CLI von einer Onlineebene auf die Archivebene zu kopieren, rufen Sie den Befehl [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) auf und geben Sie die Archivebene an. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="bulk-archive"></a>Massenarchiv

Wenn Sie eine große Anzahl von Blobs auf die Archivebene verschieben, verwenden Sie einen Batchvorgang, um eine optimale Leistung zu gewährleisten. Ein Batchvorgang sendet mehrere API-Befehle mit einer einzelnen Anfrage an den Dienst. Zu den vom [Blobbatchvorgang](/rest/api/storageservices/blob-batch) unterstützten Untervorgängen gehören [Blob löschen](/rest/api/storageservices/delete-blob) und [Blob-Ebene festlegen.](/rest/api/storageservices/set-blob-tier)

Um Blobs mit einem Batchvorgang zu archivieren, verwenden Sie eine der Azure Storage-Client-Bibliotheken. Das folgende Codebeispiel zeigt, wie Sie einen einfachen Batchvorgang mit der .NET-Client-Bibliothek durchführen:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

Ein ausführliches Beispiel, das zeigt, wie Sie Ebenen mit einem Batchvorgang ändern, finden Sie unter [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="see-also"></a>Siehe auch

- [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](access-tiers-overview.md)
- [Reaktivierung von Blobs aus der Archivebene](archive-rehydrate-overview.md)
- [Aktivieren eines archivierten Blobs auf einer Onlineebene](archive-rehydrate-to-online-tier.md)
