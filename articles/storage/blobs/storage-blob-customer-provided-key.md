---
title: Angeben eines vom Kunden bereitgestellten Schlüssels für eine Anforderung in Blob Storage mit .NET – Azure Storage
description: Erfahren Sie, wie Sie einen vom Kunden bereitgestellten Schlüssel für eine Anforderung in Blob Storage mit .NET angeben.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: b6f7001b9eabd34774930378d0656b4ad7ef3c25
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129857795"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Angeben eines vom Kunden bereitgestellten Schlüssels für eine Anforderung in Blob Storage mit .NET

Clients, die Anforderungen für Azure Blob Storage stellen, haben die Möglichkeit, einen AES-256-Verschlüsselungsschlüssel für eine einzelne Anforderung anzugeben. Die Einbeziehung des Verschlüsselungsschlüssels in die Anforderung ermöglicht eine präzise Steuerung der Verschlüsselungseinstellungen für Blob-Speichervorgänge. Vom Kunden bereitgestellte Schlüssel können in Azure Key Vault oder einem anderen Schlüsselspeicher gespeichert werden.

In diesem Artikel wird beschrieben, wie Sie einen vom Kunden bereitgestellten Schlüssel für eine Anforderung mit .NET angeben.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Weitere Informationen zur Authentifizierung bei der Azure Identity-Clientbibliothek finden Sie unter [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Verwenden eines vom Kunden bereitgestellten Schlüssels zum Schreiben in einen Blob

Das folgende Beispiel liefert einen AES-256-Schlüssel beim Hochladen eines Blobs mit der v12-Clientbibliothek für Blobspeicher. Im Beispiel wird das Objekt [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) verwendet, um die Schreibanforderung mit Azure AD zu autorisieren, Sie können die Anforderung jedoch auch mit den Anmeldeinformationen eines gemeinsam verwendeten Schlüssels autorisieren. Weitere Informationen zur Verwendung der Klasse „DefaultAzureCredential“ zum Autorisieren einer verwalteten Identität für den Zugriff auf Azure Storage finden Sie unter [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme).

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- [Angeben eines Verschlüsselungsschlüssels bei Richten einer Anforderung an Blob Storage](encryption-customer-provided-keys.md)
- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../common/storage-service-encryption.md)
