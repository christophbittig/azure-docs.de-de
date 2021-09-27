---
title: Anonymer Zugriff auf öffentliche Container und Blobs mit .NET
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie die Clientbibliothek für .NET von Azure Storage verwenden, um anonym auf öffentliche Container und Blobs zuzugreifen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ac8015403ffa64271990363b7a95a18ae6f57a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626796"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Anonymer Zugriff auf öffentliche Container und Blobs mit .NET

Azure Storage unterstützt den optionalen, öffentlichen Lesezugriff auf Container und Blobs. Clients können anonym auf öffentliche Container und Blobs zugreifen, indem die Clientbibliotheken von Azure Storage verwendet werden sowie weitere Tools und Hilfsprogramme, die den Datenzugriff auf Azure Storage unterstützen.

Dieser Artikel zeigt, wie Sie auf einen öffentlichen Container oder ein Blob in .NET zugreifen. Informationen zum Konfigurieren des anonymen Lesezugriffs auf einen Container finden Sie unter [Konfigurieren des anonymen öffentlichen Lesezugriffs für Container und Blobs](anonymous-read-access-configure.md). Weitere Informationen zum Verhindern jeglichen anonymen Zugriffs auf ein Speicherkonto finden Sie unter [Verhindern von anonymem öffentlichem Lesezugriff auf Container und Blobs](anonymous-read-access-prevent.md).

Ein Client, der auf Container und Blobs anonym zugreift, kann Konstruktoren verwenden, für die keine Anmeldeinformationen erforderlich sind. In den folgenden Beispielen werden verschiedene Möglichkeiten zum anonymen Verweisen auf Container und Blobs gezeigt.

## <a name="create-an-anonymous-client-object"></a>Erstellen eines anonymen Clientobjekts

Sie können ein neues Dienstclientobjekt für den anonymen Zugriff durch Bereitstellen des Blob Storage-Endpunkts für das Konto erstellen. Allerdings müssen Sie auch den Namen eines Containers in diesem Konto kennen, der für den anonymen Zugriff verfügbar ist.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties.
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Anonymes Verweisen auf einen Container

Wenn Sie über die URL zu einem Container verfügen, der anonym verfügbar ist, können Sie damit direkt auf den Container verweisen.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Anonymes Verweisen auf ein Blob

Wenn Sie über die URL zu einem Blob verfügen, das für den anonymen Zugriff verfügbar ist, können Sie damit über diese URL direkt auf das Blob verweisen:

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren des anonymen, öffentlichen Lesezugriffs auf Container und Blobs](anonymous-read-access-configure.md)
- [Verhindern von anonymem, öffentlichen Lesezugriff auf Container und Blobs](anonymous-read-access-prevent.md)
- [Autorisierung des Zugriffs auf Azure Storage](../common/authorize-data-access.md)
