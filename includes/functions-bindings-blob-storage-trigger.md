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
ms.openlocfilehash: 0eaf21a20c41952a78e37b5139adb9a993513b2d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803069"
---
### <a name="default"></a>Standard

Sie können die folgenden Parametertypen für das auslösende Blob verwenden:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

Eine Bindung mit `string` oder `Byte[]` wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) weiter unten in diesem Artikel.

### <a name="additional-types"></a>Zusätzliche Typen

Apps, die die [Version 5.0.0 oder höher der Storage-Erweiterung](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) verwenden, können auch Typen aus dem [Azure SDK für .NET](/dotnet/api/overview/azure/storage.blobs-readme) verwenden. In dieser Version wird die Unterstützung der Legacytypen `ICloudBlob`, `CloudBlockBlob`, `CloudPageBlob` und `CloudAppendBlob` zugunsten der folgenden Typen aufgegeben:

- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Beispiele für die Verwendung dieser Typen finden Sie im [GitHub-Repository für die jeweilige Erweiterung](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples). Erfahren Sie im [Azure.Storage.Blobs- Migrationshandbuch](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/storage/Azure.Storage.Blobs/AzureStorageNetMigrationV12.md) mehr über diese neuen Typen, wie sie sich unterscheiden und wie zu ihnen migrieren.
