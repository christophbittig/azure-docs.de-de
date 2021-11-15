---
title: Aktivieren eines archivierten Blobs auf einer Onlineebene
titleSuffix: Azure Storage
description: Bevor Sie einen Blob lesen können, der sich im Archiv befindet, müssen Sie ihn entweder in den heißen oder den kühlen Zustand rehydrieren. Sie können einen Blob rehydrieren, indem Sie ihn entweder von der Archiv-Ebene in eine Online-Ebene kopieren oder seine Ebene von Archiv auf Heiß oder Kühl ändern.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: 0e24c058239d57cba1c66ebff06ba9d482bcb594
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433192"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Aktivieren eines archivierten Blobs auf einer Onlineebene

Um einen Blob zu lesen, der sich in der Archivschicht befindet, müssen Sie den Blob zunächst in eine Online-Schicht (Hot oder Cool) rehydrieren. Sie haben zwei Möglichkeiten, ein Blob zu aktivieren:

- Durch Kopieren in einen neuen Blob in der heißen oder kalten Schicht mit der Operation [Blob kopieren](/rest/api/storageservices/copy-blob). Microsoft empfiehlt diese Option für die meisten Szenarien.
- Durch Ändern der Ebene von Archiv auf Heiß oder Kühl mit der Operation [Blob Ebene](/rest/api/storageservices/set-blob-tier) einstellen.

Wenn Sie einen Blob rehydrieren, können Sie die Priorität für den Vorgang entweder auf Standardpriorität oder hohe Priorität festlegen. Ein Rehydrierungsvorgang mit Standardpriorität kann bis zu 15 Stunden dauern. Ein Vorgang mit hoher Priorität hat Vorrang vor Anfragen mit Standardpriorität und kann bei Objekten mit einer Größe von weniger als 10 GB in weniger als einer Stunde abgeschlossen sein. Sie können die Rehydrierungspriorität von *Standard* auf *Hoch* ändern, während der Vorgang noch läuft.

Sie können Azure Event Grid konfigurieren, um nach Abschluss der Aktivierung ein Ereignis auszulösen und als Reaktion Anwendungscode auszuführen. Informationen zum Behandeln eines Ereignisses, bei dem eine Azure-Funktion ausgeführt wird, wenn der Blob-Aktivierungsvorgang abgeschlossen ist, finden Sie unter [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis).

Weitere Informationen zur Rehydrierung eines Blobs finden Sie unter [Blob-Rehydrierung aus der Archivebene](archive-rehydrate-overview.md).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Aktivieren eines Blobs mit einem Kopiervorgang

Um einen Blob aus dem Archive-Tier zu rehydrieren, indem er in ein Online-Tier kopiert wird, verwenden Sie PowerShell, Azure CLI oder eine der Azure Storage-Clientbibliotheken. Beachten Sie, dass beim Kopieren eines archivierten Blobs auf eine Onlineebene die Quell- und Zielblobs unterschiedliche Namen haben müssen.

Nach Abschluss des Kopiervorgangs wird der Ziel-Blob in der Archivebene angezeigt. Das Zielblob wird dann auf der Onlineebene aktiviert, die Sie im Kopiervorgang angegeben haben. Wenn das Zielblob vollständig aktiviert ist, wird es auf der neuen Onlineebene verfügbar.

In den folgenden Beispielen wird gezeigt, wie Sie ein archiviertes Blob mit PowerShell oder der Azure-Befehlszeilenschnittstelle kopieren.

### <a name="portal"></a>[Portal](#tab/azure-portal)

–

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um ein archiviertes Blob mit PowerShell auf eine Onlineebene zu kopieren, rufen Sie den Befehl [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) auf, und geben Sie die Zielebene und die Aktivierungspriorität an. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

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

Verwenden Sie das Azure-Portal, die PowerShell oder die Azure-Befehlszeilenschnittstelle, um einen Blob zu rehydrieren, indem Sie seinen Tier von Archive auf Hot oder Cool ändern.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Gehen Sie folgendermaßen vor, um im Azure-Portal den Tier eines Blobs von Archive auf Hot oder Cool zu ändern:

1. Suchen Sie das Blob, das aktiviert werden soll, im Azure-Portal.
1. Wählen Sie rechts auf der Seite die Schaltfläche **Mehr** aus.
1. Wählen Sie **Ebene ändern** aus.
1. Wählen Sie im Dropdown **Zugriffsebene** die Zielzugriffsebene aus.
1. Wählen Sie aus dem Dropdown **Aktivierungspriorität** die gewünschte Aktivierungspriorität aus. Beachten Sie, dass das Festlegen der Aktivierungspriorität auf *Hoch* in der Regel zu einer schnelleren Aktivierung führt, jedoch auch höhere Kosten verursacht.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Screenshot, der zeigt, wie ein Blob aus der Archivebene im Azure-Portal rehydriert wird ":::

1. Klicken Sie auf die Schaltfläche **Speichern**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Stufe eines Blobs mit PowerShell von Archiv auf Hot oder Cool zu ändern, verwenden Sie die Eigenschaft **BlobClient** des Blobs, um einen .NET-Verweis auf den Blob zurückzugeben, und rufen dann die Methode **SetAccessTier** für diesen Verweis auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Hot with Standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "Standard")
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Tier eines Blob von Archive auf Hot oder Cool mit Azure CLI zu ändern, rufen Sie den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) auf. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --rehydrate-priority Standard \
    --auth-mode login
```

---

## <a name="bulk-rehydrate-a-set-of-blobs"></a>Rehydrieren einer Reihe von Klecksen in großen Mengen

Um eine große Anzahl von Blobs auf einmal zu aktivieren, müssen Sie den Vorgang [Blob Batch](/rest/api/storageservices/blob-batch) aufrufen, damit [Set Blob Tier](/rest/api/storageservices/set-blob-tier) als Massenvorgang aufgerufen wird. Ein Codebeispiel, in dem die Ausführung des Batchvorgangs veranschaulicht wird, finden Sie unter [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Überprüfen des Status eines Aktivierungsvorgangs

Während das Blob aktiviert wird, können Sie seinen Status und seine Aktivierungspriorität über das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle überprüfen. Die Status-Eigenschaft gibt abhängig von der Zielebene für den Aktivierungsvorgang möglicherweise *rehydrate-pending-to-hot* oder *rehydrate-pending-to-cool* zurück. Die Aktivierungsprioritäts-Eigenschaft gibt entweder *Standard* oder *Hoch* zurück.

Beachten Sie, dass die Aktivierung eines archivierten Blobs bis zu 15 Stunden dauern kann, und das wiederholte Abrufen des Blobstatus, um festzustellen, ob die Aktivierung abgeschlossen ist, ist ineffizient. Die Verwendung von Azure Event Grid zur Erfassung des Ereignisses, das bei Abschluss der Aktivierung ausgelöst wird, bietet eine bessere Leistungs- und Kostenoptimierung. Informationen zur Ausführung einer Azure-Funktion, wenn nach der Blobaktivierung ein Ereignis ausgelöst wird, finden Sie unter [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Um den Status und die Priorität eines ausstehenden Aktivierungsvorgangs im Azure-Portal zu überprüfen, zeigen Sie das Dialogfeld **Ebene ändern** für das Blob an:

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Screenshot, der den Aktivierungsstatus für ein Blob im Azure-Portal zeigt.":::

Wenn die Aktivierung abgeschlossen ist, sehen Sie im Azure-Portal, dass das vollständig aktivierte Blob nun auf der Ziel-Onlineebene angezeigt wird.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Screenshot mit dem rehydrierten Blob in der Cool tier und dem vom Event Handler geschriebenen Log Blob":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um den Status und die Priorität eines ausstehenden Aktivierungsvorgangs mit PowerShell zu überprüfen, rufen Sie den Befehl [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) auf, und überprüfen Sie die Eigenschaften **ArchiveStatus** und **RehydratePriority** des Blobs. Wenn es sich bei der Aktivierung um einen Kopiervorgang handelt, überprüfen Sie diese Eigenschaften im Zielblob. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurepowershell
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
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="change-the-rehydration-priority-of-a-pending-operation"></a>Ändern Sie die Rehydrierungspriorität einer anstehenden Operation

Während ein Rehydrationsvorgang mit Standardpriorität läuft, können Sie die Rehydrationsprioritätseinstellung für einen Blob von *Standard* auf *Hoch* ändern, um diesen Blob schneller zu rehydrieren.

Beachten Sie, dass die Einstellung der Rehydrationspriorität nicht von *Hoch* auf *Standard* für einen anstehenden Vorgang gesenkt werden kann. Denken Sie auch daran, dass eine Änderung der Rehydratationspriorität Auswirkungen auf die Abrechnung haben kann. Weitere Informationen finden Sie unter [Blob-Rehydratisierung aus der Archivebene](archive-rehydrate-overview.md).

### <a name="change-the-rehydration-priority-for-a-pending-set-blob-tier-operation"></a>Ändern der Rehydrierungspriorität für eine anstehende Set Blob Tier Operation

Um die Rehydrierungspriorität zu ändern, während ein Vorgang mit Standardpriorität [Set Blob Tier](/rest/api/storageservices/set-blob-tier) anhängig ist, verwenden Sie das Azure-Portal, PowerShell, Azure CLI oder eine der Azure Storage-Clientbibliotheken.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Gehen Sie folgendermaßen vor, um die Rehydrierungspriorität für einen ausstehenden Vorgang über das Azure-Portal zu ändern:

1. Navigieren Sie zu dem Blob, für den Sie die Rehydrierungspriorität ändern möchten, und wählen Sie den Blob aus.
1. Wählen Sie die Schaltfläche **Ebene ändern**.
1. Setzen Sie im Dialogfeld **Ebene** ändern die Zugriffsebene auf die gewünschte Online-Zugriffsebene für den rehydrierenden Blob (Heiß oder Kühl). Das Feld **Archivstatus** zeigt die gewünschte Online-Ebene an.
1. Setzen Sie in der Dropdown-Liste **Rehydratisierungspriorität** die Priorität auf *Hoch*.
1. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/update-rehydration-priority-portal.png" alt-text="Screenshot zeigt, wie die Rehydrierungspriorität für einen rehydrierenden Blob im Azure-Portal aktualisiert werden kann":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Rehydrierungspriorität für einen ausstehenden Vorgang mit PowerShell zu ändern, stellen Sie sicher, dass Sie das [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage)-Modul, Version 3.12.0 oder höher, installiert haben. Dann rufen Sie die Eigenschaften des Blobs aus dem Dienst ab. Dieser Schritt ist notwendig, um sicherzustellen, dass Sie ein Objekt mit den neuesten Eigenschaftseinstellungen haben. Verwenden Sie anschließend die Eigenschaft **BlobClient** des Blobs, um einen .NET-Verweis auf den Blob zurückzugeben, und rufen Sie dann die Methode **SetAccessTier** für diesen Verweis auf.

```azurepowershell
# Get the blob from the service.
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

# Verify that the current rehydration priority is Standard. 
if ($rehydratingBlob.BlobProperties.RehydratePriority -eq "Standard")
{
    # Change rehydration priority to High, using the same target tier.
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-hot")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Hot", $null, "High")
        "Changing rehydration priority to High for blob moving to Hot tier."
    }
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-cool")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Cool", $null, "High")
        "Changing rehydration priority to High for blob moving to Cool tier."
    }
}
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Rehydrierungspriorität für einen ausstehenden Vorgang mit der Azure CLI zu ändern, stellen Sie zunächst sicher, dass Sie die Azure CLI, Version 2.29.2 oder höher, installiert haben. Weitere Informationen zur Installation der Azure CLI finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Rufen Sie als Nächstes den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) auf, wobei der Parameter `--rehydrate-priority` auf *Hoch* festgelegt ist. Die Zielebene (Heiß oder Kühl) muss dieselbe Ebene sein, die Sie ursprünglich für den Rehydrierungsvorgang angegeben haben. Denken Sie daran, Platzhalter in spitzen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
# Update the rehydration priority for a blob moving to the Hot tier.
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Hot \
    --rehydrate-priority High \
    --auth-mode login

# Show the updated property values.
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

### <a name="change-the-rehydration-priority-for-a-pending-copy-blob-operation"></a>Ändern Sie die Rehydrierungspriorität für einen anstehenden Kopiervorgang von Blob

Wenn Sie einen Blob rehydrieren, indem Sie den archivierten Blob in eine Online-Ebene kopieren, erstellt Azure Storage sofort den Ziel-Blob in der Archivebene. Der Ziel-Blob wird dann mit der im Kopiervorgang angegebenen Priorität in die Zielebene rehydriert. Weitere Informationen zur Rehydrierung eines archivierten Blob mit einem Kopiervorgang finden Sie unter [Kopieren eines archivierten Blob in eine Online-Ebene](archive-rehydrate-overview.md#copy-an-archived-blob-to-an-online-tier).

Verwenden Sie PowerShell, Azure CLI oder eine der Azure-Storage-Clientbibliotheken, um den Kopiervorgang vom Archive-Tier auf ein Online-Tier mit Standardpriorität durchzuführen. Weitere Informationen finden Sie unter [Blob mit einer Kopieroperation rehydrieren](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation). Um die Rehydrierungspriorität für die anstehende Rehydrierung von *Standard* auf *Hoch* zu ändern, rufen Sie als Nächstes **Set Blob Tier** auf dem Zielblob auf und geben die Zielebene an.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Nachdem Sie den Kopiervorgang initiiert haben, sehen Sie im Azure-Portal, dass sich sowohl der Quell- als auch der Ziel-Blob in der Archivebene befinden. Der Ziel-Blob wird mit Standardpriorität rehydriert.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-standard-priority.png" alt-text="Screenshot zeigt den Ziel-Blob in der Archivebene und die Rehydrierung mit Standardpriorität":::

Gehen Sie folgendermaßen vor, um die Rehydrierungspriorität für den Ziel-Blob zu ändern:

1. Wählen Sie den Ziel-Blob aus.
1. Wählen Sie die Schaltfläche **Ebene ändern**.
1. Setzen Sie im Dialogfeld **Ebene** ändern die Zugriffsebene auf die gewünschte Online-Zugriffsebene für den rehydrierenden Blob (Heiß oder Kühl). Das Feld **Archivstatus** zeigt die gewünschte Online-Ebene an.
1. Setzen Sie in der Dropdown-Liste **Rehydratisierungspriorität** die Priorität auf *Hoch*.
1. Wählen Sie **Speichern** aus.

Auf der Eigenschaftenseite des Ziel-Blob wird nun angezeigt, dass er mit hoher Priorität rehydriert wird.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-high-priority.png" alt-text="Screenshot zeigt Ziel-Blob in Archivebene und Rehydrierung mit hoher Priorität":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nachdem Sie den Kopiervorgang gestartet haben, überprüfen Sie die Eigenschaften des Zielblob. Sie werden sehen, dass sich der Ziel-Blob in der Archiv-Ebene befindet und mit Standardpriorität rehydriert wird.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$destContainerName = "<container>"
$destBlobName = "<destination-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Get properties for the destination blob.
$destinationBlob = Get-AzStorageBlob -Container $destContainerName `
    -Blob $destBlobName `
    -Context $ctx

$destinationBlob.BlobProperties.AccessTier
$destinationBlob.BlobProperties.ArchiveStatus
$destinationBlob.BlobProperties.RehydratePriority
```

Rufen Sie als Nächstes die Methode **SetAccessTier** über PowerShell auf, um die Rehydrierungspriorität für den Ziel-Blob auf *Hoch* zu ändern, wie in [Ändern der Rehydrierungspriorität für einen anstehenden Set Blob Tier-Vorgang](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation) beschrieben. Die Zielebene (Heiß oder Kühl) muss dieselbe Ebene sein, die Sie ursprünglich für den Rehydrierungsvorgang angegeben haben. Überprüfen Sie die Eigenschaften erneut, um sicherzustellen, dass der Blob jetzt mit hoher Priorität rehydriert wird.

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Nachdem Sie den Kopiervorgang gestartet haben, überprüfen Sie die Eigenschaften des Zielblob. Sie werden sehen, dass sich der Ziel-Blob in der Archiv-Ebene befindet und mit Standardpriorität rehydriert wird.

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

Rufen Sie dann den Befehl [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) mit dem Parameter `--rehydrate-priority` auf *High* auf, wie in [Ändern Sie die Rehydrierungspriorität für eine anstehende Set Blob Tier Operation](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation) beschrieben. Die Zielebene (Heiß oder Kühl) muss dieselbe Ebene sein, die Sie ursprünglich für den Rehydrierungsvorgang angegeben haben. Überprüfen Sie die Eigenschaften erneut, um sicherzustellen, dass der Blob jetzt mit hoher Priorität rehydriert wird.

---

## <a name="see-also"></a>Siehe auch

- [Heiße, kühle und Archiv-Zugriffsebenen für Blobdaten](access-tiers-overview.md).
- [Übersicht über die Rehydrierung von Klecksen aus der Archivebene](archive-rehydrate-overview.md)
- [Run an Azure Function in response to a blob rehydration event](archive-rehydrate-handle-event.md) (Ausführen einer Azure-Funktion als Reaktion auf ein Blob-Aktivierungsereignis)
- [Reacting to Blob storage events (preview)](storage-blob-event-overview.md) (Reagieren auf Blob Storage-Ereignisse (Vorschauversion))
