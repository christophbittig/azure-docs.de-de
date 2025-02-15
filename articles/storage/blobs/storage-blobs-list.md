---
title: Auflisten von Blobs mit Azure Storage-APIs
description: Erfahren Sie, wie Sie mithilfe der Azure Storage-Clientbibliotheken Blobs in Ihrem Speicherkonto auflisten. Codebeispiele veranschaulichen, wie Blobs in einer flachen Auflistung angezeigt werden oder wie Blobs hierarchisch aufgelistet werden, als wären Sie in Verzeichnissen oder Ordnern organisiert.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/24/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9dbcb4aba8b1c25aa7f00850d71872c9f58b94a9
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462030"
---
# <a name="list-blobs-with-azure-storage-client-libraries"></a>Auflisten von Blobs mit Azure Storage-Clientbibliotheken

Wenn Sie Blobs über Ihren Code auflisten, können Sie eine Reihe von Optionen angeben, um zu steuern, wie Ergebnisse von Azure Storage zurückgegeben werden. Sie können die Anzahl der Ergebnisse festlegen, die in den einzelnen Ergebnissätzen zurückgegeben werden sollen, und dann die nachfolgenden Sätze abrufen. Sie können ein Präfix angeben, um Blobs zurückzugeben, deren Namen mit dem jeweiligen Zeichen oder der Zeichenfolge beginnen. Sie können Blobs auch in einer flachen Auflistungsstruktur anzeigen oder hierarchisch auflisten. Bei einer hierarchischen Auflistung werden Blobs so zurückgegeben, als wären sie in Ordnern organisiert.

## <a name="understand-blob-listing-options"></a>Grundlegendes zu den Optionen für das Auflisten von Blobs

Rufen Sie zum Auflisten der Blobs in einem Speicherkonto eine der folgenden Methoden auf:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

- [BlobContainerClient.GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

- [CloudBlobClient.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Rufen Sie zum Auflisten der Blobs in einem Container eine der folgenden Methoden auf:

- [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer.ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

- [ContainerClient.list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)

---

### <a name="manage-how-many-results-are-returned"></a>Festlegen der Anzahl der zurückgegebenen Ergebnisse

Standardmäßig gibt ein Auflistungsvorgang bis zu 5000 Ergebnisse in einem Durchgang zurück, Sie können jedoch die Anzahl der Ergebnisse angeben, die von jedem Auflistungsvorgang zurückgegeben werden soll. Die Beispiele in diesem Artikel zeigen Ihnen, wie Ergebnisse in Seiten zurückgegeben werden.

### <a name="filter-results-with-a-prefix"></a>Filtern von Ergebnissen mit einem Präfix

Um die Liste der Blobs zu filtern, geben Sie für den `prefix`-Parameter eine Zeichenfolge an. Die Präfixzeichenfolge kann ein oder mehrere Zeichen enthalten. Dann gibt Azure Storage nur die Blobs zurück, deren Namen mit diesem Präfix beginnen.

### <a name="return-metadata"></a>Zurückgeben von Metadaten

Sie können zusammen mit den Ergebnissen Blobmetadaten zurückgeben.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Geben Sie den **Metadatenwert** für die [BlobTraits](/dotnet/api/azure.storage.blobs.models.blobtraits)-Enumeration an.

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Geben Sie den **Metadatenwert** für die [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails)-Enumeration an. Weil Azure Storage Metadaten für jedes zurückgegebene Blob enthält, müssen Sie in diesem Kontext keine der **FetchAttributes**-Methoden aufrufen, um die Blobmetadaten abzurufen.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

Geben Sie `metadata` für den Parameter `include=` an, wenn Sie [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)aufrufen.

---

### <a name="list-blob-versions-or-snapshots"></a>Auflisten von Blobversionen oder-Momentaufnahmen

- Geben Sie zum Auflisten von Blobversionen oder Momentaufnahmen bei der .NET v12-Clientbibliothek den Parameter [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) im Feld **Version** oder **Momentaufnahme** an. Versionen und Momentaufnahmen werden von der ältesten zur neuesten aufgeführt. Weitere Informationen zum Auflisten von Versionen finden Sie unter [Auflisten von Blobversionen](versioning-enable.md#list-blob-versions).

- Um die Anzahl der Momentaufnahmen mit der Python v12-Clientbibliothek aufzulisten, geben Sie `num_snapshots` beim Aufruf von [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) im Parameter `include=` an.

### <a name="flat-listing-versus-hierarchical-listing"></a>Flache Auflistung und hierarchische Auflistung im Vergleich

Blobs in Azure Storage sind in einem flachen Paradigma organisiert statt in einem hierarchischen Paradigma (wie ein klassisches Dateisystem). Sie können Blobs jedoch in *virtuellen Verzeichnissen* organisieren, um eine Ordnerstruktur zu imitieren. Ein virtuelles Verzeichnis bildet einen Teil des Blobnamens und wird durch das Trennzeichen angezeigt.

Wenn Sie also Blobs in virtuellen Verzeichnissen organisieren möchten, verwenden Sie ein Trennzeichen im Blobnamen. Das Standardtrennzeichen ist ein Schrägstrich (/), doch können Sie ein beliebiges Zeichen als Trennzeichen angeben.

Wenn Sie Ihre Blobs mithilfe eines Trennzeichens benennen, können Sie sie hierarchisch auflisten. Bei einem hierarchischen Auflistungsvorgang gibt Azure Storage alle virtuellen Verzeichnisse und Blobs unter dem übergeordneten Objekt zurück. Sie können den Auflistungsvorgang rekursiv aufrufen, um die Hierarchie zu durchlaufen – ähnlich wie beim programmgesteuerten Durchlaufen eines klassischen Dateisystems.

## <a name="use-a-flat-listing"></a>Verwenden einer flachen Auflistung

Ein Auflistungsvorgang gibt Blobs standardmäßig in einer flachen Auflistung zurück. In einer flachen Auflistung werden Blobs nicht nach virtuellem Verzeichnis organisiert.

Im folgenden Beispiel werden die Blobs im angegebenen Container mithilfe einer flachen Auflistung aufgeführt, wobei eine optionale Segmentgröße angegeben und der Blobname in ein Konsolenfenster geschrieben wird.

Wenn Sie für Ihr Konto das Feature für hierarchische Namespaces aktiviert haben, sind Verzeichnisse nicht virtuell. Vielmehr handelt es sich um konkrete, unabhängige Objekte. Daher werden Verzeichnisse in der Liste als Blobs mit der Länge 0 (null) angezeigt.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Wenn ein Auflistungsvorgang mehr als 5000 Blobs zurückgibt oder die Anzahl der verfügbaren Blobs die von Ihnen angegebene Anzahl überschreitet, gibt Azure Storage ein *Fortsetzungstoken* mit der Liste der Blobs zurück. Ein Fortsetzungstoken ist ein nicht transparenter Wert, den Sie verwenden können, um den nächsten Satz von Ergebnissen aus Azure Storage abzurufen.

Überprüfen Sie im Code den Wert des Fortsetzungstokens, um zu bestimmen, ob er NULL ist. Wenn das Fortsetzungstoken NULL ist, ist der Satz der Ergebnisse vollständig. Wenn das Fortsetzungstoken nicht NULL ist, rufen Sie den Auflistungsvorgang erneut auf, und übergeben Sie das Fortsetzungstoken, um den nächsten Ergebnissatz so oft abzurufen, bis das Fortsetzungstoken NULL ist.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_ListBlobs":::

---

Die Beispielausgabe sieht ähnlich wie hier aus:

```console
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Verwenden einer hierarchischen Auflistung

Wenn Sie einen Auflistungsvorgang hierarchisch aufrufen, gibt Azure Storage die virtuellen Verzeichnisse und Blobs auf der ersten Hiearchieebene zurück.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Um Blobs hierarchisch aufzulisten, müssen Sie die Methode [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy) oder [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) aufrufen.

Im folgenden Beispiel werden die Blobs im angegebenen Container mithilfe einer hierarchischen Auflistung aufgeführt, wobei eine optionale Segmentgröße angegeben und der Blobname in das Konsolenfenster geschrieben wird.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Die Eigenschaft [Prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) der einzelnen virtuellen Verzeichnisse ist so festgelegt, dass Sie das Präfix in einem rekursiven Aufruf übergeben können, um das nächste Verzeichnis abzurufen.

Wenn Blobs hierarchisch aufgelistet werden sollen, legen Sie den Parameter `useFlatBlobListing` der Auflistungsmethode auf **false** fest.

Im folgenden Beispiel werden die Blobs im angegebenen Container mithilfe einer flachen Auflistung aufgeführt, wobei eine optionale Segmentgröße angegeben und der Blobname in das Konsolenfenster geschrieben wird.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

Um Blobs hierarchisch aufzulisten, rufen Sie die Methode [walk_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#walk-blobs-name-starts-with-none--include-none--delimiter--------kwargs-) auf.

Im folgenden Beispiel werden die Blobs im angegebenen Container mithilfe einer hierarchischen Auflistung aufgeführt, wobei eine optionale Segmentgröße angegeben und der Blobname in das Konsolenfenster geschrieben wird.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_WalkHierarchy":::

---

Die Beispielausgabe sieht ähnlich wie hier aus:

```console
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> Blobmomentaufnahmen können nicht in einem hierarchischen Auflistungsvorgang aufgeführt werden.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Auflisten von Blobs](/rest/api/storageservices/list-blobs)
- [Aufzählen von Blobressourcen](/rest/api/storageservices/enumerating-blob-resources)