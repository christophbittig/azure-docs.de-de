---
title: Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Tabellen und Warteschlangen unterstützt
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie ein Speicherkonto erstellen, das die Konfiguration von kundenseitig verwalteten Schlüsseln für Tabellen und Warteschlangen unterstützt. Verwenden Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) oder eine Azure Resource Manager-Vorlage zum Erstellen eines Speicherkontos, das den Kontoverschlüsselungsschlüssel zur Azure Storage-Verschlüsselung verwendet. Anschließend können Sie kundenseitig verwaltete Schlüssel für das Konto konfigurieren.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/09/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9ab311c8e348c6e0cbb703a73562770a2f4f963f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251739"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Erstellen eines Kontos, das kundenseitig verwaltete Schlüssel für Tabellen und Warteschlangen unterstützt

Azure Storage verschlüsselt alle Daten in einem ruhenden Speicherkonto. Standardmäßig verwenden Queue Storage und Table Storage einen Schlüssel, der dem Dienst zugeordnet ist und von Microsoft verwaltet wird. Sie können auch kundenseitig verwaltete Schlüssel verwenden, um Warteschlangen- oder Tabellendaten zu verschlüsseln. Wenn Sie kundenseitig verwaltete Schlüssel bei Warteschlangen und Tabellen verwenden möchten, müssen Sie zuerst ein Speicherkonto erstellen, das einen dem Konto (und nicht dem Dienst) zugeordneten Verschlüsselungsschlüssel verwendet. Nachdem Sie ein Speicherkonto erstellt haben, das den Kontoverschlüsselungsschlüssel für Warteschlangen- und Tabellendaten verwendet, können Sie kundenseitig verwaltete Schlüssel für dieses Konto konfigurieren.

In diesem Artikel wird beschrieben, wie Sie ein Speicherkonto erstellen, das einen dem Konto zugeordneten Schlüssel verwendet. Wenn das Konto zum ersten Mal erstellt wird, verwendet Microsoft den Kontoschlüssel zum Verschlüsseln der Daten im Konto und verwaltet diesen Schlüssel auch. Anschließend können Sie kundenseitig verwaltete Schlüssel für das Konto konfigurieren, um diese Vorteile zu nutzen – einschließlich der Möglichkeit, Ihre eigenen Schlüssel bereitzustellen, die Schlüsselversion zu aktualisieren, die Schlüssel zu rotieren und Zugriffssteuerungen zu widerrufen.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Erstellen eines Kontos, das den Kontoverschlüsselungsschlüssel verwendet

Zum Zeitpunkt der Erstellung des Speicherkontos müssen Sie ein neues Speicherkonto konfigurieren, um den Kontoverschlüsselungsschlüssel für Warteschlangen und Tabellen verwenden zu können. Der Gültigkeitsbereich des Verschlüsselungsschlüssels kann nicht mehr geändert werden, nachdem das Konto erstellt wurde.

Das Speicherkonto muss vom Typ „Allgemein v2 (GPv2)“ sein. Sie können das Speicherkonto erstellen und für die Verwendung des Kontoverschlüsselungsschlüssels konfigurieren, indem Sie das Azure-Portal, PowerShell, die Azure CLI oder eine Azure Resource Manager-Vorlage verwenden.

Weitere Informationen zum Erstellen eines Speicherkontos finden Sie unter [Speicherkonto erstellen](storage-account-create.md).

> [!NOTE]
> Nur Queue Storage und Table Storage können optional so konfiguriert werden, dass Daten beim Erstellen des Speicherkontos mit dem Kontoverschlüsselungsschlüssel verschlüsselt werden. Blob Storage und Azure Files verwenden immer den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Speicherkonto zu erstellen, das auf dem Kontoverschlüsselungsschlüssel basiert:

1. Wählen Sie im Menü des linken Portals **Speicherkonten** aus, um eine Liste Ihrer Speicherkonten anzuzeigen.
1. Klicken Sie auf der Seite **Speicherkonten** auf **Neu**.
1. Füllen Sie die Felder auf der Registerkarte **Grundlagen** aus.
1. Navigieren Sie auf der Registerkarte „Erweitert“ zum Abschnitt **Tabellen und Warteschlangen**, und wählen Sie **Unterstützung für kundenseitig verwaltete Schlüssel aktivieren** aus.

    :::image type="content" source="media/account-encryption-key-create/enable-cmk-tables-queues.png" alt-text="Screenshot: Aktivieren von kundenseitig verwalteten Schlüsseln für Warteschlangen und Tabellen beim Erstellen eines neuen Kontos":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie über PowerShell ein Speicherkonto erstellen möchten, das den Kontoverschlüsselungsschlüssel verwendet, muss die Azure PowerShell-Modulversion 3.4.0 oder höher installiert sein. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2 (GPv2)“, indem Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) mit den entsprechenden Parametern aufrufen:

- Fügen Sie die Option `-EncryptionKeyTypeForQueue` ein, und legen Sie deren Wert auf `Account` fest, um den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten in Queue Storage verwenden zu können.
- Fügen Sie die Option `-EncryptionKeyTypeForTable` ein, und legen Sie deren Wert auf `Account` fest, um den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten in Table Storage verwenden zu können.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff konfiguriert ist und den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten für Queue Storage und Table Storage verwendet. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) ein Speicherkonto erstellen möchten, das den Kontoverschlüsselungsschlüssel verwendet, müssen Sie Azure CLI, Version 2.0.80 oder höher, installiert haben. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Allgemein v2 (GPv2)“, indem Sie den Befehl „[az storage account create](/cli/azure/storage/account#az_storage_account_create)“ mit den entsprechenden Parametern aufrufen:

- Fügen Sie die Option `--encryption-key-type-for-queue` ein, und legen Sie deren Wert auf `Account` fest, um den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten in Queue Storage verwenden zu können.
- Fügen Sie die Option `--encryption-key-type-for-table` ein, und legen Sie deren Wert auf `Account` fest, um den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten in Table Storage verwenden zu können.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff konfiguriert ist und den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten für Queue Storage und Table Storage verwendet. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Vorlage](#tab/template)

Mit dem folgenden JSON-Beispiel wird ein Speicherkonto vom Typ „Universell v2“ erstellt, das für georedundanten Speicher mit Lesezugriff konfiguriert ist und den Kontoverschlüsselungsschlüssel zum Verschlüsseln von Daten für Queue Storage und Table Storage verwendet. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Nachdem Sie ein Konto erstellt haben, das den Kontoverschlüsselungsschlüssel verwendet, können Sie kundenseitig verwaltete Schlüssel konfigurieren, die in Azure Key Vault oder in Key Vault Managed Hardware Security Model (HSM) gespeichert werden. Informationen zum Speichern von kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md). Informationen zum Speichern von kundenseitig verwalteten Schlüsseln in einem verwalteten HSM finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Überprüfen des Kontoverschlüsselungsschlüssels

Nachdem Sie das Konto erstellt haben, können Sie mithilfe des Azure-Portals, von PowerShell oder der Azure CLI überprüfen, ob das Speicherkonto einen dem Konto zugeordneten Verschlüsselungsschlüssel verwendet.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Um im Azure-Portal zu überprüfen, ob ein Dienst in einem Speicherkonto einen dem Konto zugeordneten Verschlüsselungsschlüssel verwendet, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrem neuen Speicherkonto.
1. Wählen Sie im Abschnitt **Sicherheit + Netzwerkbetrieb** die Option **Verschlüsselung** aus.
1. Wenn das Speicherkonto für die Verwendung des Kontoverschlüsselungsschlüssels erstellt wurde, sehen Sie auf der Registerkarte **Verschlüsselung**, dass kundenseitig verwaltete Schlüssel für alle vier Azure Storage-Dienste aktiviert werden können: Blob, File, Table und Queue.

    :::image type="content" source="media/account-encryption-key-create/verify-cmk-tables-queues.png" alt-text="Screenshot: Überprüfen, ob das Speicherkonto den Kontoverschlüsselungsschlüssel verwendet":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie mit PowerShell überprüfen möchten, ob ein Dienst in einem Speicherkonto den Kontoverschlüsselungsschlüssel verwendet, rufen Sie den Befehl [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) auf. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Überprüfen Sie, ob das Feld `KeyType` der einzelnen Dienste innerhalb der Eigenschaft `Encryption` auf `Account` festgelegt ist.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie mit der Azure CLI überprüfen möchten, ob ein Dienst in einem Speicherkonto den Kontoverschlüsselungsschlüssel verwendet, rufen Sie den Befehl [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Suchen Sie in der Eigenschaft „Encryption“ bei jedem Dienst nach dem Feld `keyType`, und überprüfen Sie, ob dessen Wert auf `Account` festgelegt ist.

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

–

---

Nachdem Sie sichergestellt haben, dass das Speicherkonto einen dem Konto zugeordneten Verschlüsselungsschlüssel verwendet, können Sie kundenseitig verwaltete Schlüssel für das Konto aktivieren. Alle vier Azure Storage-Dienste (Blob, File, Table und Queue) verwenden dann den kundenseitig verwalteten Schlüssel für die Verschlüsselung.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für ein Speicherkonto, das erstellt wird, um einen Verschlüsselungsschlüssel zu verwenden, der dem Konto zugeordnet ist, werden die Tabellenspeicherkapazität und Transaktionen mit einer anderen Rate in Rechnung gestellt als bei einem Konto, das den dienstbezogenen Standardschlüssel verwendet. Weitere Informationen finden Sie unter [Preise für Azure Table Storage](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](customer-managed-keys-overview.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](customer-managed-keys-configure-key-vault.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM (Vorschau) gespeichert sind](customer-managed-keys-configure-key-vault-hsm.md)