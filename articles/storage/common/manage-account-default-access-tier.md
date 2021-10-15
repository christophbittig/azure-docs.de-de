---
title: Verwalten der Standardzugriffsebene für ein Azure Storage-Konto
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die Standardzugriffsebene für ein Universell v2- oder Blob Storage-Konto ändern können.
author: tamram
ms.author: tamram
ms.date: 09/23/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a9cb4a119447188202036cca4ed4d8580329d0cc
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272564"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Verwalten der Standardzugriffsebene für ein Azure Storage-Konto

Bei jedem Azure Storage-Konto gibt es eine Standardzugriffsebene („Heiß“ oder „Kalt“). Sie weisen die Zugriffsebene beim Erstellen eines Speicherkontos zu. Die Standardzugriffsebene ist „Heiß“.

Sie können die Standardkontoebene ändern, indem Sie für das Speicherkonto das Attribut **Zugriffsebene** festlegen. Die Änderung der Zugriffsebene gilt für alle im Konto gespeicherten Objekte, für die keine explizite Ebene festgelegt wurde. Wenn Sie die Kontoebene von „Heiß“ in „Kalt“ ändern, fallen Gebühren für Schreibvorgänge (pro 10.000) nur für alle Blobs ohne festgelegte Ebene in Universell v2-Konten an. Bei der Umstellung von „Kalt“ auf „Heiß“ fallen Gebühren für Lesevorgänge (pro 10.000) und den Datenabruf (pro GB) für alle Blobs in Blob Speicher- und Universell v2-Konten an.

Für Blobs, bei denen die Ebene auf Objektebene festgelegt wurde, gilt die Kontoebene nicht. Die Archivebene kann nur auf Objektebene angewendet werden. Sie können jederzeit zwischen Zugriffsebenen wechseln.

Nachdem ein Speicherkonto erstellt wurde, können Sie die Standardzugriffsebene ändern, indem Sie die Schritte unten ausführen.

## <a name="change-the-default-account-access-tier-of-a-general-purpose-v2-or-blob-storage-account"></a>Ändern der Standard-Kontozugriffsebene für ein Universell v2- oder Blob Storage-Konto

In den folgenden Szenarien wird das Azure-Portal oder PowerShell verwendet:

# <a name="portal"></a>[Portal](#tab/portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Azure-Portal nach **Alle Ressourcen**, und wählen Sie den Eintrag aus.

1. Wählen Sie dann Ihr Speicherkonto aus.

1. Wählen Sie unter **Einstellungen** die Option **Konfiguration** aus, um die Kontokonfiguration anzuzeigen und zu ändern.

1. Wählen Sie die passende Zugriffsebene für Ihre Anforderungen aus: Legen Sie die **Zugriffsebene** entweder auf **kalte Ebene** oder auf **heiße Ebene** fest.

1. Klicken Sie oben auf **Speichern** .

![Ändern der Standardkontoebene im Azure-Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Das folgende PowerShell-Skript kann zum Ändern der Kontoebene verwendet werden. Die Variable `$rgName` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden.

```powershell
# Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

# Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azurecli)

Das folgende Azure CLI-Skript kann zum Ändern der Kontoebene verwendet werden. Die Variable `$rgName` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden.

```azurecli
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
az storage account update --resource-group $rgName --name $accountName --access-tier Hot
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten der Ebene eines Blobs in einem Azure Storage-Konto](../blobs/manage-access-tier.md)
- [Ermitteln, ob Premium-Leistung für Ihre App vorteilhaft ist](../blobs/storage-blob-performance-tiers.md)
- [Auswerten der Nutzung vorhandener Speicherkonten durch Aktivierung von Azure Storage-Metriken](../blobs/monitor-blob-storage.md)
