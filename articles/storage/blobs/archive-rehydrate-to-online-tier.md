---
title: Aktivieren eines archivierten Blobs auf einer Onlineebene
titleSuffix: Azure Storage
description: Bevor Sie ein Blob lesen können, das sich auf der Archivebene befindet, müssen Sie es entweder auf der heißen oder der kalten Ebene aktivieren. Sie können ein Blob entweder durch Kopieren von der Archivebene auf eine Onlineebene oder durch Ändern seiner Ebene von „Archiv“ in „Heiß“ oder „Kalt“ aktivieren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: a1681640b97d6ee183eefd7f65edd9b2d944f0f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633866"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Aktivieren eines archivierten Blobs auf einer Onlineebene

Um ein Blob zu lesen, das sich auf der Archivebene befindet, müssen Sie das Blob zuerst auf der heißen oder der kalten Ebene aktivieren. Sie haben zwei Möglichkeiten, ein Blob zu aktivieren:

- Durch Kopieren in ein neues Blob auf der heißen oder kalten Ebene mit dem Vorgang [Copy Blob](/rest/api/storageservices/copy-blob) (Blob kopieren) oder [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) (Blob aus URL kopieren). Microsoft empfiehlt diese Option für die meisten Szenarien.
- Durch Ändern der Ebene von „Archiv“ in „Heiß“ oder „Kalt“ mit dem Vorgang [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blobebene festlegen).

Ein Aktivierungsvorgang kann bis zu 15 Stunden in Anspruch nehmen. Sie können Azure Event Grid konfigurieren, um nach Abschluss der Aktivierung ein Ereignis auszulösen und als Reaktion Anwendungscode auszuführen. Informationen zum Behandeln eines Ereignisses, bei dem eine Azure-Funktion ausgeführt wird, wenn der Blob-Aktivierungsvorgang abgeschlossen ist, finden Sie unter [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Aktivieren eines Blobs mit einem Kopiervorgang

Um ein Blob aus der Archivebene zu aktivieren, indem Sie es auf eine Onlineebene kopieren, verwenden Sie PowerShell, die Azure-Befehlszeilenschnittstelle oder eine der Azure Storage-Clientbibliotheken. Beachten Sie, dass beim Kopieren eines archivierten Blobs auf eine Onlineebene die Quell- und Zielblobs unterschiedliche Namen haben müssen.

Nach Abschluss des Kopiervorgangs wird das Zielblob auf der Archivebene angezeigt. Das Zielblob wird dann auf der Onlineebene aktiviert, die Sie im Kopiervorgang angegeben haben. Wenn das Zielblob vollständig aktiviert ist, wird es auf der neuen Onlineebene verfügbar.

In den folgenden Beispielen wird gezeigt, wie Sie ein archiviertes Blob mit PowerShell oder der Azure-Befehlszeilenschnittstelle kopieren.

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um ein archiviertes Blob mit PowerShell auf eine Onlineebene zu kopieren, rufen Sie den Befehl [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) auf, und geben Sie die Zielebene und die Aktivierungspriorität an. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
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

# Copy the source blob to a new destination blob in hot tier with standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um ein archiviertes Blob mit der Azure-Befehlszeilenschnittstelle auf eine Onlineebene zu kopieren, rufen Sie den Befehl [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) auf, und geben Sie die Zielebene und die Aktivierungspriorität an. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>Aktivieren eines Blobs durch Ändern seiner Ebene

Um ein Blob zu aktivieren, indem Sie seine Ebene von „Archiv“ in „Heiß“ oder „Kalt“ ändern, verwenden Sie das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle.

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um die Ebene eines Blobs im Azure-Portal von „Archiv“ in „Heiß“ oder „Kalt“ zu ändern:

1. Suchen Sie das Blob, das aktiviert werden soll, im Azure-Portal.
1. Wählen Sie rechts auf der Seite die Schaltfläche **Mehr** aus.
1. Wählen Sie **Ebene ändern** aus.
1. Wählen Sie im Dropdown **Zugriffsebene** die Zielzugriffsebene aus.
1. Wählen Sie aus dem Dropdown **Aktivierungspriorität** die gewünschte Aktivierungspriorität aus. Beachten Sie, dass das Festlegen der Aktivierungspriorität auf *Hoch* in der Regel zu einer schnelleren Aktivierung führt, jedoch auch höhere Kosten verursacht.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Screenshot, der zeigt, wie ein Blob aus der Archivebene im Azure-Portal aktiviert wird.":::

1. Klicken Sie auf die Schaltfläche **Speichern**.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um die Ebene eines Blobs mit PowerShell von „Archiv“ in „Heiß“ oder „Kalt“ zu ändern, verwenden Sie die Eigenschaft **ICloudBlob** des Blobs, um eine .NET-Referenz für das Blob zurückzugeben, und rufen Sie dann die Methode **SetStandardBlobTier** für diese Referenz auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Ebene eines Blobs mit der Azure-Befehlszeilenschnittstelle von „Archiv“ in „Heiß“ oder „Kalt“ zu ändern, rufen Sie den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>Aktivieren einer großen Anzahl von Blobs

Um eine große Anzahl von Blobs auf einmal zu aktivieren, müssen Sie den Vorgang [Blob Batch](/rest/api/storageservices/blob-batch) aufrufen, damit [Set Blob Tier](/rest/api/storageservices/set-blob-tier) als Massenvorgang aufgerufen wird. Ein Codebeispiel, in dem die Ausführung des Batchvorgangs veranschaulicht wird, finden Sie unter [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Überprüfen des Status eines Aktivierungsvorgangs

Während das Blob aktiviert wird, können Sie seinen Status und seine Aktivierungspriorität über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle überprüfen. Die Status-Eigenschaft gibt abhängig von der Zielebene für den Aktivierungsvorgang möglicherweise *rehydrate-pending-to-hot* oder *rehydrate-pending-to-cool* zurück. Die Aktivierungsprioritäts-Eigenschaft gibt entweder *Standard* oder *Hoch* zurück.

Beachten Sie, dass die Aktivierung eines archivierten Blobs bis zu 15 Stunden dauern kann, und das wiederholte Abrufen des Blobstatus, um festzustellen, ob die Aktivierung abgeschlossen ist, ist ineffizient. Die Verwendung von Azure Event Grid zur Erfassung des Ereignisses, das bei Abschluss der Aktivierung ausgelöst wird, bietet eine bessere Leistungs- und Kostenoptimierung. Informationen zur Ausführung einer Azure-Funktion, wenn nach der Blobaktivierung ein Ereignis ausgelöst wird, finden Sie unter [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis).

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Um den Status und die Priorität eines ausstehenden Aktivierungsvorgangs im Azure-Portal zu überprüfen, zeigen Sie das Dialogfeld **Ebene ändern** für das Blob an:

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Screenshot, der den Aktivierungsstatus für ein Blob im Azure-Portal zeigt.":::

Wenn die Aktivierung abgeschlossen ist, sehen Sie im Azure-Portal, dass das vollständig aktivierte Blob nun auf der Ziel-Onlineebene angezeigt wird.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Screenshot, der das aktivierte Blob auf der kalten Ebene sowie das vom Ereignishandler geschriebene Protokoll-Blob zeigt.":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um den Status und die Priorität eines ausstehenden Aktivierungsvorgangs mit PowerShell zu überprüfen, rufen Sie den Befehl [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) auf, und überprüfen Sie die Eigenschaften **ArchiveStatus** und **RehydratePriority** des Blobs. Wenn es sich bei der Aktivierung um einen Kopiervorgang handelt, überprüfen Sie diese Eigenschaften im Zielblob. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Status und die Priorität eines ausstehenden Aktivierungsvorgangs mit der Azure-Befehlszeilenschnittstelle zu überprüfen, rufen Sie den Befehl [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) auf, und überprüfen Sie die Eigenschaften **rehydrationStatus** und **rehydratePriority** des Zielblobs. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>Weitere Informationen

- [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](storage-blob-storage-tiers.md)
- [Overview of blob rehydration from the archive tier](archive-rehydrate-overview.md) (Übersicht über die Aktivierung von Blobs aus der Archivebene)
- [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis)
- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion))
