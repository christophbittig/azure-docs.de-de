---
title: Erstellen einer gespeicherten Zugriffsrichtlinie mit .NET
titleSuffix: Azure Storage
description: In diesem Artikel erhalten Sie Informationen zur Nutzung von Azure Storage und .NET für das Erstellen einer gespeicherten Zugriffsrichtlinie. Außerdem erhalten Sie Informationen zu verschiedenen Steuerungsebenen für auf Dienstebene freigegebenen Zugriffssignaturen auf dem Server.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/12/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c1b6aa0d6bdd98a66a638a216f93a6d81bd6b93
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726858"
---
# <a name="create-a-stored-access-policy-with-net"></a>Erstellen einer gespeicherten Zugriffsrichtlinie mit .NET

Eine gespeicherte Zugriffsrichtlinie bietet eine zusätzliche Steuerungsebene für Shared Access Signatures (SAS) der Dienstebene auf Serverseite. Das Definieren einer gespeicherten Zugriffsrichtlinie dient zum Gruppieren von Shared Access Signatures und zum Bereitstellen zusätzlicher Einschränkungen für Shared Access Signatures, die von der Richtlinie gebunden werden. Mithilfe der gespeicherten Zugriffsrichtlinie können Sie die Startzeit, die Ablaufzeit oder die Berechtigungen für eine Shared Access Signature ändern oder die Signatur widerrufen, nachdem sie ausgegeben wurde.
  
Die folgenden Azure Storage-Ressourcen unterstützen gespeicherte Zugriffsrichtlinien:  
  
- Blobcontainer  
- Dateifreigaben  
- Warteschlangen  
- Tabellen  
  
> [!NOTE]
> Eine gespeicherte Zugriffsrichtlinie für einen Container kann einer Shared Access Signature zugeordnet werden, die dem Container selbst oder den darin enthaltenen Blobs Berechtigungen erteilt. Entsprechend kann eine gespeicherte Zugriffsrichtlinie für eine Dateifreigabe einer Shared Access Signature zugeordnet werden, die der Freigabe selbst oder den darin enthaltenen Dateien Berechtigungen erteilt.  
>
> Gespeicherte Zugriffsrichtlinien werden nur für Dienst-SAS unterstützt. Gespeicherte Zugriffsrichtlinien werden für die Konto-SAS oder die SAS für die Benutzerdelegierung nicht unterstützt.  

Weitere Informationen zu gespeicherten Zugriffsrichtlinien finden Sie unter [Definieren einer gespeicherten Zugriffsrichtlinie](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Erstellen einer gespeicherten Zugriffsrichtlinie

Der zugrunde liegende REST-Vorgang zum Erstellen einer gespeicherten Zugriffsrichtlinie ist [Set Container ACL](/rest/api/storageservices/set-container-acl). Sie müssen den Vorgang zum Erstellen einer gespeicherten Zugriffsrichtlinie über einen gemeinsam verwendeten Schlüssel autorisieren, indem Sie die Kontozugriffsschlüssel in einer Verbindungszeichenfolge verwenden. Das Autorisieren des Vorgangs **Set Container ACL** mit Azure AD-Anmeldeinformationen wird nicht unterstützt. Weitere Informationen finden Sie unter [Berechtigungen zum Aufrufen von Datenvorgängen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-data-operations).

In den folgenden Codebeispielen wird eine gespeicherte Zugriffsrichtlinie für einen Container erstellt. Sie können mit der Zugriffsrichtlinie Einschränkungen für eine Dienst-SAS für den Container oder seine Blobs festlegen.

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Rufen Sie eine der folgenden Methoden auf, um mit Version 12 der .NET-Clientbibliothek für Azure Storage eine gespeicherte Zugriffsrichtlinie für einen Container zu erstellen:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

Im folgenden Beispiel wird eine gespeicherte Zugriffsrichtlinie erstellt, die einen Tag lang gilt und Lese-/Schreibberechtigungen gewährt:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Rufen Sie eine der folgenden Methoden auf, um mit Version 12 der .NET-Clientbibliothek für Azure Storage eine gespeicherte Zugriffsrichtlinie für einen Container zu erstellen:

- [CloudBlobContainer.SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer.SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Im folgenden Beispiel wird eine gespeicherte Zugriffsrichtlinie erstellt, die einen Tag lang gilt und Lese-, Schreib- und Auflistungsberechtigungen gewährt:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Weitere Informationen

- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md)
- [Definieren einer gespeicherten Zugriffsrichtlinie](/rest/api/storageservices/define-stored-access-policy)
- [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](storage-configure-connection-string.md)
