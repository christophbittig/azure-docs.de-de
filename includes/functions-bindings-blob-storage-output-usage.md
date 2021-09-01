---
title: Datei einfügen
description: include file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 4a25423cbdd5b0efbd037138a3cdf072cb90ebed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801605"
---
### <a name="default"></a>Standard

Sie können sich an die folgenden Typen binden, um Blobs zu schreiben:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> Erfordert die in-Bindung `direction` in *function.json* oder `FileAccess.Read` in einer C#-Klassenbibliothek. Allerdings können Sie das Containerobjekt verwenden, das die Runtime für Schreibvorgänge bereitstellt (z.B. zum Hochladen von Blobs in den Container).

<sup>2</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

Eine Bindung mit `string` oder `Byte[]` wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) weiter oben in diesem Artikel.

### <a name="additional-types"></a>Zusätzliche Typen

Apps, die die [Version 5.0.0 oder höher der Storage-Erweiterung](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) verwenden, können auch Typen aus dem [Azure SDK für .NET](/dotnet/api/overview/azure/storage.blobs-readme) verwenden. In dieser Version wird Unterstützung für die Legacytypen `CloudBlobContainer`, `CloudBlobDirectory`, `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` und `CloudAppendBlob` zugunsten der folgenden Typen aufgegeben:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> Erfordert die in-Bindung `direction` in *function.json* oder `FileAccess.Read` in einer C#-Klassenbibliothek. Allerdings können Sie das Containerobjekt verwenden, das die Runtime für Schreibvorgänge bereitstellt (z.B. zum Hochladen von Blobs in den Container).

<sup>2</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Beispiele für die Verwendung dieser Typen finden Sie im [GitHub-Repository für die jeweilige Erweiterung](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples). Erfahren Sie im [Azure.Storage.Blobs- Migrationshandbuch](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md) mehr über diese neuen Typen, wie sie sich unterscheiden und wie zu ihnen migrieren.
