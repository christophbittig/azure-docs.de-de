---
title: Abrufen von Informationen zur Speicherkontokonfiguration
titleSuffix: Azure Storage
description: Verwenden Sie das Azure-Portal, PowerShell oder die Azure CLI, um die Eigenschaften der Speicherkontokonfiguration abzurufen, z. B. Azure Resource Manager-Ressourcen-ID, Kontospeicherort, Kontotyp oder Replikations-SKU.
services: storage
author: tamram
ms.author: tamram
ms.date: 06/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: f3f94dda19f11b1a0aad9a84e7ae624e41c5015c
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573650"
---
# <a name="get-storage-account-configuration-information"></a>Abrufen von Informationen zur Speicherkontokonfiguration

In diesem Artikel wird beschrieben, wie Sie Konfigurationsinformationen und Eigenschaften für ein Azure Storage-Konto mit dem Azure-Portal, PowerShell oder der Azure CLI abrufen.

## <a name="get-the-resource-id-for-a-storage-account"></a>Abrufen der Ressourcen-ID für ein Speicherkonto

Jede Azure Resource Manager-Ressource verfügt über eine zugeordnete Ressourcen-ID für die eindeutige Identifikation. Für bestimmte Vorgänge ist es erforderlich, dass Sie die Ressourcen-ID angeben. Sie können die Ressourcen-ID für ein Speicherkonto abrufen, indem Sie das Azure-Portal, PowerShell oder die Azure CLI verwenden.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um die Azure Resource Manager-Ressourcen-ID für ein Speicherkonto im Azure-Portal anzuzeigen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Grundlegende Features** den Link **JSON-Ansicht** aus.
1. Die Ressourcen-ID für das Speicherkonto wird oben auf der Seite angezeigt.

    :::image type="content" source="media/storage-account-get-info/resource-id-portal.png" alt-text="Screenshot: Kopieren der Ressourcen-ID für das Speicherkonto aus dem Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Stellen Sie sicher, dass Sie das Modul [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) installiert haben, wenn Sie die Azure Resource Manager-Ressourcen-ID für ein Speicherkonto mit PowerShell zurückgeben möchten. Rufen Sie als Nächstes den Befehl [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) auf, um das Speicherkonto zurückzugeben und die zugehörige Ressourcen-ID abzurufen:

```azurepowershell
(Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>).Id
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie den Befehl [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf, und fragen Sie die Ressourcen-ID ab, um die Azure Resource Manager-Ressourcen-ID für ein Speicherkonto mit der Azure CLI zurückzugeben:

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query id \
    --output tsv
```

---

Sie können die Ressourcen-ID für ein Speicherkonto auch abrufen, indem Sie in der REST-API den Vorgang zum [Abrufen von Eigenschaften für Speicherkonten (Storage Accounts – Get Properties)](/rest/api/storagerp/storage-accounts/get-properties) aufrufen.

Weitere Informationen zu den Arten von Ressourcen, die mit Azure Resource Manager verwaltet werden, finden Sie unter [Ressourcenanbieter und Ressourcentypen](../../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="get-the-account-type-location-or-replication-sku-for-a-storage-account"></a>Abrufen des Kontotyps, des Speicherorts oder der Replikations-SKU für ein Speicherkonto

Der Kontotyp, der Speicherort und die Replikations-SKU sind einige der Eigenschaften, die für ein Speicherkonto verfügbar sind. Sie können diese Angaben im Azure-Portal, mit PowerShell oder über die Azure CLI anzeigen.

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um den Kontotyp, den Speicherort oder die Replikations-SKU für ein Speicherkonto im Azure-Portal anzuzeigen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Suchen Sie nach diesen Eigenschaften auf der Seite **Übersicht** im Abschnitt **Grundlegende Features**.

    :::image type="content" source="media/storage-account-get-info/account-configuration-portal.png" alt-text="Screenshot: Kontokonfiguration im Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie zum Anzeigen des Kontotyps, des Speicherorts oder der Replikations-SKU für ein Speicherkonto mit PowerShell den Befehl [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) auf, um das Speicherkonto zurückzugeben, und überprüfen Sie anschließend die Eigenschaften:

```azurepowershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> -Name <storage-account>
$account.Location
$account.Sku
$account.Kind
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Rufen Sie zum Anzeigen des Kontotyps, des Speicherorts oder der Replikations-SKU für ein Speicherkonto mit PowerShell den Befehl [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf, und fragen Sie die Eigenschaften ab:

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query '[location,sku,kind]' \
    --output tsv
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Speicherkontoübersicht](storage-account-overview.md)
- [Erstellen eines Speicherkontos](storage-account-create.md)
