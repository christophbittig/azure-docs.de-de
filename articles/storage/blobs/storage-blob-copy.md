---
title: Kopieren eines Blobs mit Azure Storage-APIs
description: Erfahren Sie, wie Sie ein Blob mithilfe der Azure Storage-Clientbibliotheken kopieren.
services: storage
author: normesta
ms.author: normesta
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 39969e5e1767bbd2a84a27bb5cbbea6d51c9a4eb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674564"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Kopieren eines Blobs mit Azure Storage-Clientbibliotheken

In diesem Artikel wird beschrieben, wie Sie ein Blob in einem Azure Storage-Konto kopieren. Außerdem wird gezeigt, wie ein asynchroner Kopiervorgang abgebrochen wird. Im Beispielcode werden die Azure Storage-Clientbibliotheken verwendet.

## <a name="about-copying-blobs"></a>Informationen zum Kopieren von Blobs

Wenn Sie ein Blob innerhalb desselben Speicherkontos kopieren, ist dies ein synchroner Vorgang. Wenn Sie zwischen Konten kopieren, ist es ein asynchroner Vorgang.

Das Quellblob für einen Kopiervorgang kann ein Blockblob, ein Anfügeblob, ein Seitenblob oder eine Momentaufnahme sein. Wenn das Zielblob bereits vorhanden ist, muss es von demselben Blobtyp wie das Quellblob sein. Ein eventuell vorhandenes Zielblob wird überschrieben.

Das Zielblob kann während der Ausführung eines Kopiervorgangs nicht geändert werden. Bei einem Zielblob kann es nur einen ausstehenden Kopiervorgang geben. Anders ausgedrückt: Ein Blob kann nicht das Ziel für mehrere ausstehende Kopiervorgänge sein.

Es wird immer das gesamte Quellblob oder die gesamte Quelldatei kopiert. Das Kopieren eines Bytebereichs oder einer Gruppe von Blöcken wird nicht unterstützt.

Wenn ein Blob kopiert wird, werden dessen Systemeigenschaften mit denselben Werten in das Zielblob kopiert.

Ein Kopiervorgang kann eine der folgenden Formen haben:

- Kopieren Sie ein Quellblob in ein Zielblob mit einem anderen Namen. Das Zielblob kann ein vorhandenes Blob desselben Blobtyps sein (Blockblob, Anfügeblob oder Seitenblob), oder es kann sich um ein neues Blob handeln, das durch den Kopiervorgang erstellt wurde.
- Kopieren Sie ein Quellblob in ein Zielblob mit demselben Namen, wodurch das Zielblob tatsächlich ersetzt wird. Ein solcher Kopiervorgang entfernt alle Blöcke ohne Commit und überschreibt die Metadaten des Zielblobs.
- Kopieren Sie eine Quelldatei im Azure-Dateidienst in ein Zielblob. Das Zielblob kann ein vorhandenes Blockblob oder ein neues Blockblob sein, das durch den Kopiervorgang erstellt wurde. Das Kopieren von Dateien in Seitenblobs oder Anfügeblobs wird nicht unterstützt.
- Kopieren Sie eine Momentaufnahme über das zugehörige Basis-Blob. Indem Sie eine Momentaufnahme zu einem Basis-Blob heraufstufen, können Sie eine frühere Version eines Blobs wiederherstellen.
- Kopieren Sie eine Momentaufnahme in ein Zielblob mit einem anderen Namen. Das resultierende Zielblob ist ein beschreibbares Blob und keine Momentaufnahme.

## <a name="copy-a-blob"></a>Kopieren eines Blobs

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Zum Kopieren eines Blobs rufen Sie eine der folgenden Methoden auf:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

Die Methoden `StartCopyFromUri` und `StartCopyFromUriAsync` geben ein [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation)-Objekt zurück, das Informationen über den Kopiervorgang enthält.

Im folgenden Codebeispiel wird ein [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) abgerufen, der ein zuvor erstelltes Blob darstellt und in demselben Container in ein neues Blob kopiert:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Zum Kopieren eines Blobs rufen Sie eine der folgenden Methoden auf:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

Die Methoden `StartCopy` und `StartCopyAsync` geben einen Wert für die Kopier-ID zurück, der zum Überprüfen des Status oder zum Abbrechen des Kopiervorgangs verwendet wird.

Im folgenden Codebeispiel wird ein Verweis auf ein zuvor erstelltes Blob abgerufen und in ein neues Blob in demselben Container kopiert:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

Um ein Blob zu kopieren, rufen Sie die [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-)-Methode auf. Die `start_copy_from_url`-Methode gibt ein Wörterbuch zurück, das Informationen über den Kopiervorgang enthält.

Im folgenden Codebeispiel wird ein [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) abgerufen, der ein zuvor erstelltes Blob darstellt und in demselben Container in ein neues Blob kopiert:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Abbrechen eines Kopiervorgangs

Ein Abbrechen eines Kopiervorgangs führt zu einem Zielblob der Länge null. Die Metadaten für das Zielblob weisen jedoch die neuen Werte auf, die aus dem Quellblob kopiert oder explizit während des Kopiervorgangs festgelegt wurden. Zur Beibehaltung der ursprünglichen, vor dem Kopiervorgang vorliegenden Metadaten erstellen Sie eine Momentaufnahme des Zielblobs, bevor Sie eine der Kopiermethoden aufrufen.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Überprüfen Sie die [BlobProperties.CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus)-Eigenschaft im Zielblob, um den Status des Kopiervorgangs zu erfahren. Das endgültige Blob wird bei Abschluss des Kopiervorgangs committet.

Wenn Sie einen Kopiervorgang abbrechen, wird der Kopierstatus des Zielblobs auf [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus) festgelegt.

Mit den Methoden [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) und [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) wird ein laufender Kopiervorgang abgebrochen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Überprüfen Sie die [CopyState.Status](/dotnet/api/microsoft.azure.storage.blob.copystate.status)-Eigenschaft im Zielblob, um den Status des Kopiervorgangs zu erfahren. Das endgültige Blob wird bei Abschluss des Kopiervorgangs committet.

Wenn Sie einen Kopiervorgang abbrechen, wird der Kopierstatus des Zielblobs auf [CopyStatus.Aborted](/dotnet/api/microsoft.azure.storage.blob.copystatus) festgelegt.

Mit den Methoden [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) und [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) wird ein laufender Kopiervorgang abgebrochen.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

Überprüfen Sie den Eintrag „status“ im [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties)-Wörterbuch, das von der [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-)-Methode zurückgegeben wurde, um den Status des Kopiervorgangs zu erfahren. Das endgültige Blob wird bei Abschluss des Kopiervorgangs committet.

Wenn Sie einen Kopiervorgang abbrechen, wird [status](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) auf „aborted“ festgelegt.

Mit der [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-)-Methode wird ein laufender Kopiervorgang abgebrochen.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDKs

Weitere Informationen zu Azure SDKs:

- [Azure SDK für .NET](https://github.com/azure/azure-sdk-for-net)
- [Azure SDK für Java](https://github.com/azure/azure-sdk-for-java)
- [Azure-SDK für Python](https://github.com/azure/azure-sdk-for-python)
- [Azure SDK für JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Themen enthalten Informationen zum Kopieren von Blobs und zum Abbrechen laufender Kopiervorgänge mithilfe der Azure-REST-APIs.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Abort Copy Blob](/rest/api/storageservices/abort-copy-blob)
