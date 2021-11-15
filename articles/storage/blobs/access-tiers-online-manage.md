---
title: Festlegen der Zugriffsebene eines Blobs
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die Zugriffsebene eines Blobs beim Hochladen angeben oder die Zugriffsebene für ein vorhandenes Blob ändern.
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7cd01ff89ffdf01676a9a7c3ef5d41ac1bf138a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441493"
---
# <a name="set-a-blobs-access-tier"></a>Festlegen der Zugriffsebene eines Blobs

Sie haben folgende Möglichkeiten, um die Zugriffsebene eines Blobs festzulegen:

- Durch Festlegen der standardmäßigen Onlinezugriffsebene („Heiß“ oder „Kalt“) für das Speicherkonto. Blobs im Konto erben diese Zugriffsebene, sofern die Einstellung für ein einzelnes Blob nicht explizit außer Kraft gesetzt wird.
- Durch explizites Festlegen der Ebene eines Blobs beim Hochladen. Sie können ein Blob auf der Ebene „Heiß“, „Kalt“ oder „Archiv“ erstellen.
- Durch Ändern der Ebene eines vorhandenen Blobs mit dem Vorgang „Set Blob Tier“, normalerweise um von einer heißen Ebene zu einer kälteren zu wechseln.
- Durch Kopieren eines Blobs mit dem Vorgang „Copy Blob“, normalerweise um von einer kalten Ebene zu einer heißeren zu wechseln.

In diesem Artikel wird beschrieben, wie Sie ein Blob in einer Onlinezugriffsebene („Heiß“ oder „Kalt“) verwalten. Weitere Informationen zum Verschieben eines Blobs in die Archivebene finden Sie unter [Archivieren eines Blobs](archive-blob.md). Weitere Informationen zum Wiederaktivieren eines Blobs aus der Archivebene finden Sie im Artikel zum [Aktivieren eines archivierten Blobs in einer Onlineebene](archive-rehydrate-to-online-tier.md).

Weitere Informationen zu den Zugriffsebenen für Blobs finden Sie unter [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](access-tiers-overview.md).

## <a name="set-the-default-access-tier-for-a-storage-account"></a>Festlegen der Standardzugriffsebene für ein Speicherkonto

Die Standardeinstellung für die Zugriffsebene für ein universelles V2-Speicherkonto bestimmt, in welcher Onlineebene ein neues Blob standardmäßig erstellt wird. Sie können die Standardzugriffsebene für ein universelles V2-Speicherkonto beim Erstellen des Kontos oder durch Aktualisieren der Konfiguration eines vorhandenen Kontos festlegen.

Wenn Sie die Standardeinstellung für die Zugriffsebene für ein vorhandenes universelles V2-Speicherkonto ändern, gilt die Änderung für alle Blobs im Konto, für die explizit keine Zugriffsebene festgelegt wurde. Die Änderung der Standardzugriffsebene kann sich auf die Abrechnung auswirken. Weitere Informationen finden Sie unter [Einstellung für die Standardzugriffsebene eines Kontos](access-tiers-overview.md#default-account-access-tier-setting).

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die Standardzugriffsebene für ein Speicherkonto bei der Erstellung im Azure-Portal festzulegen:

1. Navigieren Sie zur Seite **Speicherkonten**, und wählen Sie die Schaltfläche **Erstellen** aus.
1. Füllen Sie die Registerkarte **Grundlagen** aus.
1. Legen Sie die **Zugriffsebene** auf der Registerkarte **Erweitert** unter **Blobspeicher** auf *Heiß* oder *Kalt* fest. Die Standardeinstellung ist *Heiß*.
1. Wählen Sie **Überprüfen und erstellen** aus, um die Einstellungen zu überprüfen und das Speicherkonto zu erstellen.

    :::image type="content" source="media/access-tiers-online-manage/set-default-access-tier-create-portal.png" alt-text="Screenshot: Festlegen der Standardzugriffsebene beim Erstellen eines Speicherkontos.":::

Führen Sie die folgenden Schritte aus, um die Standardzugriffsebene für ein vorhandenes Speicherkonto im Azure-Portal zu aktualisieren:

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto.
1. Klicken Sie unter **Einstellungen** auf **Konfiguration**.
1. Suchen Sie die Einstellung **Blobzugriffsebene (Standard)** , und wählen Sie *Heiß* oder *Kalt* aus. Die Standardeinstellung ist *Heiß*, sofern diese Eigenschaft noch nicht festgelegt wurde.
1. Speichern Sie die Änderungen.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Standardeinstellung für die Zugriffsebene für ein Speicherkonto mit PowerShell zu ändern, rufen Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, und geben Sie die neue Standardzugriffsebene an.

```azurepowershell
$rgName = <resource-group>
$accountName = <storage-account>

# Change the storage account tier to Cool
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Cool
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Standardeinstellung für die Zugriffsebene für ein Speicherkonto mit PowerShell zu ändern, rufen Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, und geben Sie die neue Standardzugriffsebene an.

```azurecli
# Change the storage account tier to Cool
az storage account update \
    --resource-group <resource-group> \
    --name <storage-account> \
    --access-tier Cool
```

---

## <a name="set-a-blobs-tier-on-upload"></a>Festlegen der Ebene eines Blobs beim Hochladen

Wenn Sie ein Blob in Azure Storage hochladen, haben Sie zwei Möglichkeiten zum Festlegen der Ebene eines Blobs beim Hochladen:

- Sie können explizit die Ebene angeben, in der das Blob erstellt wird. Diese Einstellung setzt die Standardzugriffsebene für das Speicherkonto außer Kraft. Sie können die Ebene für ein Blob oder mehrere Blobs beim Hochladen in „Heiß“, „Kalt“ oder „Archiv“ festlegen.
- Sie können ein Blob hochladen, ohne eine Ebene anzugeben. In diesem Fall wird das Blob in der für das Speicherkonto angegebenen Standardzugriffsebene erstellt („Heiß“ oder „Kalt“).

In den folgenden Abschnitten wird beschrieben, wie Sie angeben, ob ein Blob in die Ebene „Heiß“ oder „Kalt“ hochgeladen wird. Weitere Informationen zum Archivieren eines Blobs beim Hochladen finden Sie unter [Archivieren von Blobs beim Hochladen](archive-blob.md#archive-blobs-on-upload).

### <a name="upload-a-blob-to-a-specific-online-tier"></a>Hochladen eines Blobs in eine bestimmte Onlineebene

Um ein Blob in der Ebene „Heiß“ oder „Kalt“ zu erstellen, geben Sie die betreffende Ebene beim Erstellen des Blobs an. Die beim Hochladen angegebene Zugriffsebene setzt die Standardzugriffsebene für das Speicherkonto außer Kraft.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um ein Blob oder mehrere Blobs im Azure-Portal in eine bestimmte Ebene hochzuladen:

1. Navigieren Sie zum Zielcontainer.
1. Wählen Sie die Schaltfläche **Hochladen**.
1. Wählen Sie die Datei oder die Dateien aus, die Sie hochladen möchten.
1. Erweitern Sie den Abschnitt **Erweitert**, und legen Sie die **Zugriffsebene** auf *Heiß* oder *Kalt* fest.
1. Wählen Sie die Schaltfläche **Hochladen**.

    :::image type="content" source="media/access-tiers-online-manage/upload-blob-to-online-tier-portal.png" alt-text="Screenshot: Hochladen von Blobs in eine Onlineebene im Azure-Portal.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um ein Blob oder mehrere Blobs mit PowerShell in eine bestimmte Ebene hochzuladen, rufen Sie den Befehl [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Cool tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Cool

# Upload the contents of a sample-blobs directory to the Cool tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Cool
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um ein Blob mit Azure CLI in eine bestimmte Ebene hochzuladen, rufen Sie den Befehl [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Cool \
    --auth-mode login
```

Um mehrere Blobs mit Azure CLI in eine bestimmte Ebene hochzuladen, rufen Sie den Befehl [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="upload-a-blob-to-the-default-tier"></a>Hochladen eines Blobs in die Standardebene

Speicherkonten verfügen über eine Einstellung für die Standardzugriffsebene, mit der die Onlineebene angegeben wird, auf der ein neues Blob erstellt wird. Die Einstellung für die Standardzugriffsebene kann auf die heiße oder die kalte Ebene festgelegt werden. Das Verhalten dieser Einstellung unterscheidet sich abhängig vom Typ des Speicherkontos geringfügig:

- Die Standardzugriffsebene für ein neues universelles V2-Speicherkonto ist standardmäßig auf die heiße Ebene festgelegt. Sie können die Einstellung für die Standardzugriffsebene beim oder nach dem Erstellen eines Speicherkontos ändern.
- Wenn Sie ein Blob Storage-Legacy-Konto erstellen, müssen Sie die Einstellung für die Standardzugriffsebene bei der Erstellung des Speicherkontos auf „Heiß“ oder „Kalt“ festlegen. Sie können die Einstellung für die Standardzugriffsebene nach der Erstellung eines Speicherkontos ändern.

Für ein Blob, dem keine explizite Ebene zugewiesen ist, wird die zugehörige Ebene von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet. Im Azure-Portal, mit PowerShell oder über die Azure-Befehlszeilenschnittstelle können Sie ermitteln, ob die Zugriffsebene eines Blobs abgeleitet ist.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn die Zugriffsebene eines Blobs von der Einstellung für die Standardzugriffsebene des Kontos abgeleitet ist, wird im Azure-Portal als Zugriffsebene **Heiß (abgeleitet)** oder **Kalt (abgeleitet)** angezeigt.

:::image type="content" source="media/access-tiers-online-manage/default-access-tier-portal.png" alt-text="Screenshot: Blobs mit der Standardzugriffsebene im Azure-Portal.":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie das Blob ab, um die Zugriffsebene eines Blobs in Azure PowerShell zu bestimmen und zu ermitteln, ob es abgeleitet ist, und überprüfen Sie dann die Eigenschaften **AccessTier** und **AccessTierInferred**.

```azurepowershell
$rgName = "<resource-group>"
$storageAccount = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context.
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Get the blob from the service.
$blob = Get-AzStorageBlob -Context $ctx -Container $containerName -Blob $blobName

# Check the AccessTier and AccessTierInferred properties.
# If the access tier is inferred, that property returns true.
$blob.BlobProperties.AccessTier
$blob.BlobProperties.AccessTierInferred
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie das Blob ab, um die Zugriffsebene eines Blobs in Azure CLI zu bestimmen und zu ermitteln, ob es abgeleitet ist, und überprüfen Sie dann die Eigenschaften **blobTier** und **blobTierInferred**.

```azurecli
az storage blob show \
    --container-name <container> \
    --name <blob> \
    --account-name <storage-account> \
    --query '[properties.blobTier, properties.blobTierInferred]' \
    --output tsv \
    --auth-mode login 
```

---

## <a name="move-a-blob-to-a-different-online-tier"></a>Verschieben eines Blobs in eine andere Onlineebene

Sie haben zwei Möglichkeiten, um die Ebene eines vorhandenen Blobs zu ändern:

- Durch Aufrufen des Vorgangs [Set Blob Tier](/rest/api/storageservices/set-blob-tier), entweder direkt oder über eine Richtlinie zur [Lebenszyklusverwaltung](access-tiers-overview.md#blob-lifecycle-management) zum Ändern der Ebene des Blobs.
- Durch Aufrufen des Vorgangs [Copy Blob](/rest/api/storageservices/copy-blob) zum Kopieren eines Blobs zwischen Ebenen. In diesem Fall verbleibt das Quellblob auf der ursprünglichen Ebene, und auf der Zielebene wird ein neues Blob erstellt.

Weitere Informationen zu den jeweiligen Möglichkeiten finden Sie unter [Festlegen oder Ändern der Ebene eines Blobs](access-tiers-overview.md#setting-or-changing-a-blobs-tier).

Sie können ein Blob mit PowerShell, Azure CLI oder einer der Azure Storage-Clientbibliotheken in eine andere Ebene verschieben.

### <a name="change-a-blobs-tier"></a>Ändern der Ebene eines Blobs

Wenn Sie die Ebene eines Blobs ändern, wird das betreffende Blob mit allen zugehörigen Daten in die Zielebene verschoben. Der Aufruf von [Set Blob Tier](/rest/api/storageservices/set-blob-tier) ist in der Regel die beste Option, um die Ebene eines Blobs von einer heißen in eine kältere Ebene zu ändern.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die Ebene eines Blobs im Azure-Portal von „Heiß“ in „Kalt“ zu ändern:

1. Navigieren Sie zu dem Blob, für das Sie die Ebene ändern möchten.
1. Wählen Sie das Blob und dann die Schaltfläche **Ebene ändern** aus.
1. Wählen Sie im Dialogfeld **Ebene ändern** die Zielebene aus.
1. Klicken Sie auf die Schaltfläche **Speichern**.

    :::image type="content" source="media/access-tiers-online-manage/change-blob-tier-portal.png" alt-text="Screenshot: Ändern der Ebene eines Blobs im Azure-Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Ebene eines Blobs mit PowerShell von „Heiß“ in „Kalt“ zu ändern, verwenden Sie die Eigenschaft **BlobClient** des Blobs, um einen .NET-Verweis auf das Blob zurückzugeben, und rufen Sie dann die Methode **SetAccessTier** für diesen Verweis auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

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

# Change the blob's access tier to Cool.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Cool", $null, "Standard")
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Ebene eines Blobs mit der Azure-Befehlszeilenschnittstelle von „Heiß“ in „Kalt“ zu ändern, rufen Sie den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="copy-a-blob-to-a-different-online-tier"></a>Kopieren eines Blobs in eine andere Onlineebene

Wenn Sie ein Blob in eine andere Ebene kopieren, wird das betreffende Blob mit allen zugehörigen Daten in die Zielebene kopiert. Der Aufruf von [Copy Blob](/rest/api/storageservices/copy-blob) wird für die meisten Szenarien empfohlen, bei denen Sie ein Blob von der kalten in die heißen Ebene verschieben oder ein Blob wieder aus der Archivebene aktivieren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

–

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rufen Sie den Befehl [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) auf, und geben Sie die Zielebene an, um ein Blob mit PowerShell von der kalten Ebene in die heiße Ebene zu kopieren. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

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
    -StandardBlobTier Hot `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie den Befehl [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) auf, und geben Sie die Zielebene an, um ein Blob über die Azure-Befehlszeilenschnittstelle von der kalten Ebene in die heiße Ebene zu kopieren. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --auth-mode login
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Standard-Kontozugriffsebene für ein Azure Storage-Konto](../common/manage-account-default-access-tier.md)
- [Aktivieren eines archivierten Blobs auf einer Onlineebene](archive-rehydrate-to-online-tier.md)
