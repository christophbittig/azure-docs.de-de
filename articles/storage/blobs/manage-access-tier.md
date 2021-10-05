---
title: Verwalten der Zugriffsebene eines Blobs in einem Azure Storage-Konto
description: Es wird beschrieben, wie Sie die Ebene eines Blobs in einem GPv2- oder Blob Storage-Konto ändern.
author: tamram
ms.author: tamram
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51bad8290bea95374349bfd00b69a4c6474b226a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596940"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Verwalten der Zugriffsebene eines Blobs in einem Azure Storage-Konto

Jedes Blob verfügt über eine Standardzugriffsebene („Heiß“, „Kalt“ oder „Archiv“). Für ein Blob wird die Standardzugriffsebene des Azure Storage-Kontos übernommen, unter dem es erstellt wird. Blob Storage- und GPv2-Konten machen das Attribut **Zugriffsebene** auf Kontoebene verfügbar. Mit diesem Attribut wird die Standardzugriffsebene für alle Blobs angegeben, für die diese auf der Objektebene nicht explizit festgelegt wurde. Für Objekte, bei denen die Ebene auf Objektebene festgelegt ist, gilt die Kontoebene nicht. Die Archivebene kann nur auf der Objektebene angewendet werden. Sie können jederzeit zwischen den Zugriffsebenen wechseln, indem Sie die unten angegebenen Schritte ausführen.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Ändern der Ebene eines Blobs in einem GPv2- oder Blob Storage-Konto

In den folgenden Szenarien wird das Azure-Portal oder PowerShell verwendet:

# <a name="portal"></a>[Portal](#tab/portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Azure-Portal nach **Alle Ressourcen**, und wählen Sie den Eintrag aus.

1. Wählen Sie dann Ihr Speicherkonto aus.

1. Wählen Sie Ihren Container und dann Ihr Blob aus.

1. Wählen Sie unter **Blob-Eigenschaften** die Option **Ebene ändern** aus.

1. Wählen Sie die Zugriffsebene **Heiß**, **Kalt** oder **Archiv** aus. Wenn sich Ihr Blob derzeit im Archiv befindet und Sie es auf einer Onlineebene aktivieren möchten, können Sie auch eine der Aktivierungsprioritäten **Standard** oder **Hoch** auswählen.

1. Wählen Sie unten **Speichern** aus.

![Ändern der Blobebene im Azure-Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende PowerShell-Skript kann zum Ändern der Blobebene verwendet werden. Die Variable `$rgName` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden. Die Variable `$containerName` muss mit Ihrem Containernamen initialisiert werden. Die Variable `$blobName` muss mit Ihrem Blobnamen initialisiert werden.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob's access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Standard-Kontozugriffsebene für ein Azure Storage-Konto](../common/manage-account-default-access-tier.md)
- [Informationen zum Aktivieren von Blobdaten aus der Archivzugriffsebene](archive-rehydrate-overview.md)
- Prüfen der Preise für „Hot“, „Cool“ und „Archiv“ in Blob Storage- und GPv2-Konten nach Region unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/)
