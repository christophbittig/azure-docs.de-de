---
title: Aktivieren und Verwalten von Blobversionsverwaltung
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie Blobversionsverwaltung im Azure-Portal und mit einer Azure Resource Manager-Vorlage aktivieren können.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/07/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: bcf69aba20a53e101cf85cf8ad9e249ca72d3a93
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470488"
---
# <a name="enable-and-manage-blob-versioning"></a>Aktivieren und Verwalten von Blobversionsverwaltung

Sie können die Blob Storage-Versionsverwaltung aktivieren, um frühere Versionen eines Blobs automatisch zu verwalten, wenn er geändert oder gelöscht wird. Wenn die Blobversionsverwaltung aktiviert ist, können Sie eine frühere Version eines Blobs wiederherstellen, um Daten wiederherzustellen, wenn diese irrtümlich geändert oder gelöscht wurden.

In diesem Artikel wird gezeigt, wie Sie die Blobversionsverwaltung für das Speicherkonto über das Azure-Portal oder mithilfe einer Azure Resource Manager-Vorlage aktivieren oder deaktivieren können. Informationen zur Blobversionsverwaltung finden Sie unter [Blobversionsverwaltung](versioning-overview.md).

## <a name="enable-blob-versioning"></a>Aktivieren der Blobversionsverwaltung

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

So aktivieren Sie die Blobversionsverwaltung für ein Speicherkonto im Azure-Portal

1. Navigieren Sie im Portal zu Ihrem Speicherkonto.
1. Wählen Sie unter **Blob-Dienst** die Option **Datenschutz** aus.
1. Wählen Sie im Abschnitt **Versionsverwaltung** die Option **Aktiviert** aus.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Screenshot: Aktivieren von Blobversionsverwaltung im Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um die Blobversionsverwaltung für ein Speicherkonto mit PowerShell zu aktivieren, installieren Sie zunächst das [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage)-Modul, Version 2.3.0 oder höher. Rufen Sie dann den Befehl [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) auf, um die Versionsverwaltung zu aktivieren, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Blobversionsverwaltung für ein Speicherkonto mit der Azure CLI zu aktivieren, installieren Sie zunächst die Azure CLI-Version 2.2.0 oder höher. Rufen Sie dann den Befehl [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) auf, um die Versionsverwaltung zu aktivieren, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Vorlage](#tab/template)

Um Blobversionsverwaltung mit einer Vorlage zu aktivieren, erstellen Sie eine Vorlage, in der die **IsVersioningEnabled**-Eigenschaft auf **TRUE** festgelegt ist. Die folgenden Schritte beschreiben, wie eine Vorlage im Azure-Portal erstellt wird.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**.
1. Geben Sie in **Marketplace durchsuchen** den Begriff **Vorlagenbereitstellung** ein, und drücken Sie dann die **EINGABETASTE**.
1. Wählen Sie **Vorlagenbereitstellung** aus, klicken Sie auf **Erstellen**, und wählen Sie dann **Eigene Vorlage im Editor erstellen** aus.
1. Fügen Sie folgenden JSON-Code im Vorlagen-Editor ein. Ersetzen Sie den Platzhalter `<accountName>` durch den Namen Ihres Speicherkontos.
1. Speichern Sie die Vorlage.
1. Geben Sie die Ressourcengruppe des Kontos an, und wählen Sie dann die Schaltfläche **Kaufen** aus, um die Vorlage bereitzustellen und die Blobversionsverwaltung zu aktivieren.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Weitere Informationen zum Bereitstellen von Ressourcen mit Vorlagen im Azure-Portal finden Sie unter [Bereitstellen von Ressourcen mit dem Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Ändern eines Blobs zum Auslösen einer neuen Version

Im folgenden Codebeispiel wird veranschaulicht, wie Sie die Erstellung einer neuen Version mit der Azure Storage-Clientbibliothek für .NET ab Version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) auslösen. Stellen Sie vor dem Ausführen dieses Beispiels sicher, dass Sie Versionsverwaltung für Ihr Speicherkonto aktiviert haben.

Im Beispiel wird ein Blockblob erstellt, und anschließend werden die Metadaten des Blobs aktualisiert. Durch Aktualisieren der Metadaten des Blobs wird die Erstellung einer neuen Version ausgelöst. Das Beispiel ruft die anfängliche Version und die aktuelle Version ab und zeigt, dass nur die aktuelle Version die Metadaten enthält.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Auflisten von Blobversionen

Geben Sie zum Auflisten von Blobversionen oder Momentaufnahmen mit der .NET v12-Clientbibliothek im Feld **Version** den Parameter [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) an.

Das folgende Codebeispiel zeigt, wie Sie Blobversionen mit der Azure Storage-Clientbibliothek für .NET ab Version [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) auflisten. Stellen Sie vor dem Ausführen dieses Beispiels sicher, dass Sie Versionsverwaltung für Ihr Speicherkonto aktiviert haben.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Nächste Schritte

- [Blobversionsverwaltung](versioning-overview.md)
- [Vorläufiges Löschen für Azure Storage-Blobs](./soft-delete-blob-overview.md)
